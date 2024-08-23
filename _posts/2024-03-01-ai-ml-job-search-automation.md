---
title: AI/ML Job Search Automation
author: david
date: 2024-03-01 00:00:00 -0500
last_modified_at: 2024-05-20 00:00:00 -0500
categories: [Problem Solving, Automation]
tags: [ai/ml, automation, job serch, web scraping]
image:
  path: /assets/img/Abundance-of-Documents_AI.jpeg
  alt: An Abundance of Documents.
---

## Job Search Problems

The job search process has always been a grueling experience for me.
Maybe I should be better at it by now, but quite simply, I'm a programmer, not a marketer.
It's an exhausting grind to constantly search for and apply to open positions, leaving me drained when I'd much rather be programming "Hello World!"
The process requires consistency, to keep at it until things finally line up.

Platforms like LinkedIn have broken search functionalities, where irrelevant promoted (ad) posts are pushed to the forefront.
Given the current market saturation, job hunting feels more like a lottery.
One of my favorite quotes on the lottery is, "I’ve got about the same chance of winning by not playing."

This project attempts to find any way I can improve upon this mess through automation.

## Automation Tools For The Job

After reviewing my skills and available resources, I had decided upon what to use as the basis for building a web scraping tool:

- Home NAS
  - Jenkins instance
  - MongoDB instance
- Network and HTTP familiarity
- JavaScript experience
- Cheerio.js, a server-side JQuery for lightweight-headless HTML parsing

My NAS will handle automated scraping jobs and store the results in a local database.
The goal is to build a script that will run in the background without supervision.

I've got a history of serching through website HTTP traffic for tasks like API integration or finding specific files.
On most websites, there’s usually a way to make targeted requests for specific data.
Cheerio.js should integrate nicely, using jQuery for HTML scraping means a simple get request will allow me to extract data without the need for a browser.

Once the scraping process is up and running, the next goal is to leverage AI/ML for ranking jobs and quickly filter out those that aren't a good match.

## Development Process

The first step was deciding where to scrape for job posts.
While LinkedIn and similar platforms have an abundance of listings, they often overwhelm you with irrelevant results, making the fire-hose of data more of a hindrance than a help.
Without quality filtering, most job sites prioritize promoted ads and reposts over relevant opportunities.

I found a promising site in Built In, which offered solid search results and programmer-specific filters, making it easier to find tech jobs.
However, I discovered that Built In is very focused on specific regions, excluding my local area.
Local positions had a 5-7 day delay between their initial posting and appearance on Built In.

As a result, I’ve decided to modify my scraper to pull data from LinkedIn Jobs instead.
With the help of my AI/ML recommendation algorithm, I should be able to filter out the abundant and irrelevant results more effectively.

For the time being, my focus was simply on getting the job scraping to work on Built In.
The more advanced filtering and AI/ML integration will come later.

### Development Environment

I began development with a basic NPM project, setting up some ECMAScript Module files (.mjs) and using Cheerio.js for the initial code.
While I had a clear understanding of the logical steps needed for the scraping process, I started by downloading a single web response to process locally.
My top priority was ensuring that everything was functioning correctly before deploying the script.
The last thing I wanted was to flood the server with requests and risk getting my IP blocked.

I was able to rerun the script with small changes, only sending out web requests once everything seemed stable.
An unexpected issue arose when the Prettier formatter in my editor automatically adjusted the indentation.
This was necessary to make the HTML easier to read since the server would respond with minified files where all unnecessary formatting was removed.
The Prettier formatting helped me identify where relevant data was stored, including any hidden fields.
One particularly useful hidden field was the true "date posted," which allowed me to filter out ghost jobs showing dates years in the past.

One of my key strategies for scraping was utilizing sizable wait periods between web requests.
Because of this, I found myself relying heavily on `console.log()` commands for feedback on the script’s progress.
When running the script with live data, there could be a five or ten minute wait to re-encounter a given error.
Thus ensuring that errors were meaningful and logs clearly indicated the script's different stages became a crucial part of the development process.

TODO: xkcd compiling

After a few long sessions waiting to see that my scripts worked fully, the first stage of development was complete.

### Web Scraping Tips

- Dig into the web debugger and find out where the data is.
  - Sometimes you'll find hidden fields or a convenient cache of data.
    I found a json blob `<script type=”json”>` stored in the head of a webpage that had all the data I could hope for and some hidden data.
- Expect to either find a REST-API or some server side renderer where data is returned in the HTML page.

  - A REST-API will be the easiest, since the data you're looking for can usually be resolved to a specific data request.
  - For HTML I will find some unique anchor like an ID or Class and create a relative path for the requested data.

    - JQuery tree traversal

    ```javascript
    const timePosted = $(element).find("#main").children("div").eq(1).children("div")
        .eq(0).children("div").eq(0).children("div").eq(0).text().trim();
    ```

    - Remove irrelevant data (like button text)

    ```javascript
    $('div[data-id="view-job-button"]').remove();
    ```

    - Use .text() for grabbing the text content from a full post or a summary-preview card.
    - Convert elements to text equivalents

    ```javascript
    $(jobPostElem).find("br").after("\n");
    $(jobPostElem).find("li").after("\n").before("    ");
    $(jobPostElem).find("p").after("\n");
    ```

  - If the site is using some custom solution then just use Playwright it's gonna be easier than reverse engineering things.
    I chose my current strategy because it seemed simple and elegant but Playwright is also very lightweight and can run headlessly.

- Use async, await, promises & timeouts

```javascript
async function timeout() {
  await new Promise(r => setTimeout(r, 2000));
```

- Don't repeat yourself
  - Store ID's or create unique identifier
    - ID reuse is a thing so a secondary identification field for uniqueness may be necessary
  - Don’t re-scrape posts
  - Save early save often
    - Scripts can potentially break from web changes or being blocked, so close gracefully and save existing progress.
    - With long wait times there is no performance penalty for saving things right after they are processed.
- Glean whatever data you can.
  - Single out bullet points `<li>` in the job description.
  - Lines matching year(s) tend to reference job skills.
- Be prepared for errors with informative logging & error handling.

## Concealment With Browser Headers & Rate Limiting

During development I had researched and implemented different scraping methods.
My own twist was to have large wait times between requests and to utilize secondary sources like Google cache (RIP) or the Internet Archive.
(Note: don’t abuse the Internet Archive - they provide a vital service.)
If a website blocks your requests, don’t persist; instead, shift to a secondary source.

When making your own HTTP requests programmatically, your first line of defense is to match your request headers with those expected from a browser.
Specifically, spoofing headers such as `User-Agent`, `Accept-Language`, `Accept-Encoding`, `Accept`, and `Referer` can help your requests blend in.

It's also important to familiarize yourself with the type of rate limiting or bot detection deployed on your target website.
In my case, the data source used Cloudflare DNS for bot detection.
While most of my tactics were effective, the script occasionally encountered issues, likely due to HTTP 302 redirect messages.
Although I could have handled this edge case manually, I eventually opted to include the Python CloudScraper project.

### CloudScraper Wrapper

```python
import sys
import cloudscraper

def cloud_scrape():
    scraper = cloudscraper.create_scraper(
        browser={
            'browser': 'chrome',
            'platform': 'linux'
        }
    )
    args = sys.argv[1:]

    print(scraper.get(args[0]).text)


if __name__ == "__main__":
    cloud_scrape()

```

### Spawn Process from JS

```javascript
async function executeCMD(args = [], app = "python3") {
  return new Promise((resolve, reject) => {
    const runProcess = spawn(app, args);

    let data = "";

    // Capture stdout data
    runProcess.stdout.setEncoding("utf8");
    runProcess.stdout.on("data", (chunk) => {
      data += chunk.toString();
    });
    ...
```

Calling Python from a JavaScript project isn’t standard behavior, but with a basic wrapper for the CloudScraper library, I could spawn the process from JS and read the response from standard output.
Once set up, it seemed that my requests were flying under the radar.
At Cloudflare’s basic level of blocking, the solution proved effective and stable, as indicated by the commit history.

## Save Often & handle errors

With long waits you want to store scraped data often
Throw error to shell for Jenkins automation

## AI/ML Auto Job Ranker

had a thumbs up or down rating for jobs to create some data to train on.
Program for NLP (natural laguage parser) on job title and post-desctiption
recurring phrases of job titles
List of skills mentioned in job post.
ML rating of likely interest
assumed Supervised learning was the appropriate strategy.
looking for right ML technique for my dataset.
several examples like throw images in and train animal recognition.
landed on tabulat data ML process. This was tailored to spredseat data and could do things like movie recomendations or health survival rates vs focusing on image recognition.

dataset conformity
not all possible fields the same ex tags, phrases could be endles.
tabular data requured a fix set of data.
created a filter for minimun occurence to be a relevant phrase.
gave a finite set of data to focus on to conform with the needs of making a ML model for tabular data.
defined set of common phrases and relevant skills to look for in a job title and model weather or not they existed in a job title.
from there so long as it was a fixed set of columns the process model training could start and ended up with around an 85% accuracy.

## Future Plans

I attended a talk from someone who presented about their process for automating the application process by documenting where they applied and how they tailored their resume. I intend to contact them about integrating some of their processes to extend the scope of the toolset I've made.
