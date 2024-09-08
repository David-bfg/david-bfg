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

## Simple Browsing Jobs App

Once scraping was underway and my database began filling up with job listings, I needed a basic app to browse all the incoming jobs.

I decided to use Spring Boot with Kotlin to create a simple CRUD app.
I chose this stack because, while Spring Boot is a well-known tool, I hadn’t had any experience with it yet.
Side projects are the perfect opportunity for learning and experimenting with something new.
For me, the main goal of my projects is the experience of learning, rather than just programming as a means to an end.

With that in mind, I try and incorporate new tools alongside familiar foundations in each piece of my work.
Typically, a task will combine something familiar with something new, like JavaScript & Web scraping, Python & AI/ML, CRUD app & Spring Boot.
Making this just one more technology stack I'd be throwing at the problem.

The result was a bare-bones app for going through job posts.
On the surface it looked like the same functionality as the website I was scraping from.
The difference was, I could customize my own fields, aggregate results from multiple searches into one place and tailor the search results to my workflow.

My workflow involved skimming through new job listings, rating them as liked or disliked, and then selecting my favorite opportunities at the end of the week to apply.
Although it still required a lot of effort, this process was more efficient, condensing the work to a single weekday instead of requiring constant vigilance.

The "liked" attribute was a helpful filter for quickly accessing all the jobs I thought were high-quality.
However, with all the different job titles, I often found myself forgetting and looking them up multiple times.
There are many more positions than just the classic "software engineer."
This pain point ultimately motivated me to add AI/ML rankings to the project.

## NLP Data Filtering

Before diving into machine learning, my primary focus was on gathering the right data.
From my experience searching through various job openings, I knew there were specific keywords and phrases that caught my attention.
I used Natural Language Processing (NLP) to analyze job titles and descriptions.
Titles were examined for recurring phrases, while descriptions were analyzed through word counts to identify commonly required skills.

The result was a relatively straightforward piece of code, but it required significant manual overview to categorize the recurring phrases or words as relevant.
I sifted through hundreds of words and phrases, starting with the most common ones until the results no longer seemed relevant, labeling each with a sentiment of good, bad, or neutral.
While the coding part was simple, the manual review process was a headache.
About 90% of my time was spent acting like an assembly line, focus on data processing.
Continually determining whether some word or phrase was relevant, leaving me quite drained by the end of it.

To parse the text, I relied on the Python library NLTK (Natural Language Toolkit).
It offered convenient presets and customization options, ensuring that terms like `.net` and `node.js` were recognized as single words rather than the end of a sentence.
Although I could have replaced this with some handwritten or LLM-generated code, NLP wasn't the main focus of this project, so I chose to leverage the NLTK library and explore its features.

This project only scratched the surface of what NLTK could offer.
A helpful feature I’d appreciated was the set of stop words (like "a," "an," "and," "are," etc.) to filter out high-frequency words that exist primarily for sentence structure.

## AI/ML Auto Job Ranker

At this point, I had set up a parser to extract key metrics with the intention of using machine learning to rate job matches based on the "liked" attribute.
It seemed logical that I could derive meaningful insights from this data, but since I lacked prior experience with machine learning, I knew I had some studying to do.

I was familiar with the two main categories of machine learning: supervised and unsupervised.
This problem appeared to fit into the supervised category, as the training would be guided by my ratings of each dataset.

Based on my understanding of the dataset, I initially imagined creating a custom point system combined with statistical analysis to assign weights to each metric.
This seemed like it would be "good enough" for my purposes.
In essence, this approach is similar to what machine learning does under the hood; I just needed to figure out how to train a model using my data.
I was later surprised by how well this problem set align with machine learning, although it required significant scope limiting and data preprocessing to meet the requirements of existing tools.

As I began my AI/ML learning journey, someone recommended fast.ai as a good starting point.
It’s a free online course focused on teaching AI/ML programming in Python.

### Fast.ai Course

While AI/ML has numerous applications, such as image recognition and classification, facial recognition, character recognition, NLP, and cutting-edge technologies like Midjourney or large language models, (LLMs) my problem was inherently about data processing.
Data processing has long been a staple of computing, but it doesn’t typically capture much excitement in an introductory lecture.
It took several courses before the focus shifted from computer vision problems to data processing.

Like everyone else, I can see there are some really cool things you can do with AI, but the specific problem I’m focused on isn’t particularly flashy.
The course began to address the core concepts I was interested in about halfway through, in the fifth lecture.
This is where I learned that my problem set was classified as tabular data.

### Tabular Data

Tabular data refers to information stored in tables, such as databases or spreadsheets.
Many machine learning problems are built around tabular data, including tasks like movie recommendations or predicting health outcomes.
Even image recognition, on a larger scale, could be framed as a tabular data problem.

Here’s an example of what a tabular AI/ML problem set might look like:

| Gender | Age | Education   | Education (num) | Salary (actual) | Salary (AI/ML) |
| :----- | :-- | :---------- | :-------------- | --------------: | -------------: |
| m      | 20  | High School | 12              |          15,000 |        ~16,000 |
| f      | 33  | College     | 15              |          21,000 |        ~20,000 |
| m      | 27  | Masters     | 18              |          28,000 |        ~26,000 |
| f      | 48  | PHD         | 20              |          40,000 |        ~37,500 |

In this example, we show how each column requires a numerical representation to create an AI/ML model.
Columns are not dynamic, they can’t be added without recalculating the model and dealing with null values is a significant complication.
Categorical data, such as gender or education, is mapped to integers.
For boolean values like male or female, they are represented as 1 or 0.
For more than two categories (like education), you’d need to create a logical progression of lowest to highest, e.g., high school, college, master’s, PhD or child, teen, adult, senior.

This method describes

supervised learning
: where we have known outcomes (e.g., salary) to train the model on.

Unsupervised learning
: works with datasets that don’t have a specific outcome column, focusing on finding patterns or subgroups.

Once the model is trained, it can predict outcomes, such as estimating salary based on the given metrics.

The challenge in converting my job dataset into a tabular format was the need to "flatten" it.
Meaning, each column and row corespond to one number.
Mentally, I think of the job dataset as having a phrases column that lists all relevant phrases in a job like`[‘software’, ‘engineer’, ‘software engineer’]`.
These need to be converted into numerical representations.
A simple enumeration wasn’t feasible, phrases are random and disjoint they don’t have any logical progression, so current methods wouldn't work so easily.

Looking at some of the methods for representing new categories, it appeared that you can make up a new column so long as it represents something meaningful.
Commonly a new column will be made to represent a classification criteria.
Imagine doing a study on household struggles where you have all the needed economic data without knowing if they fall above or below the poverty line.
Well then you’d want to calculate that and add it to your dataset, to help represent such a meaningful data point.
We'll just make up a new column for each job phrase, a binary column to represent if a word or phrase is present.

This approach has limitations.
Adding new columns to tables tends to be costly, if new words appeared a new model would be created from scratch.
To manage this, I pull from a set of known relevant phrases and filter out anything having too little representation to be meaningful.

Once the data is processed, it can be transformed for AI/ML model training.
Here’s an example of flattened job data:

| Software | Engineer | Software Engineer | Lead | Liked (actual) | Liked (AI/ML) |
| :------- | :------- | :---------------- | :--- | -------------: | ------------: |
| 1        | 1        | 0                 | 0    |              1 |         ~0.75 |
| 1        | 1        | 1                 | 0    |              1 |          ~0.9 |
| 0        | 0        | 0                 | 0    |              0 |         ~0.25 |
| 1        | 1        | 1                 | 1    |              0 |          ~0.1 |

Each significant phrase is represented as a boolean value.
Related terms, like "software" and "software engineer," are processed independently, allowing the machine learning model to derive their weights separately.
With this setup, I began training models and eventually achieved around 85% accuracy.
However, as this [**XKCD comic**](https://xkcd.com/325/) humorously shows, having a 97% seller rating doesn’t tell you if 1/30 receive a Bobcat in the mail.
There's no need to mindlessly trust what the computer says and follow google maps off a bridge.
Having trained the model myself I know it’s a tool for guidance, not absolute truth.

## Jenkins Nightlys

Run scraping job at regular interval

Jenkins
Docker
NPM default plugin
Trouble adding python for CloudScraper
Built custom image to include python venv
Docker read-only, all python dependencies had to be stored within the project folder so virtual environments solved this
Other projects in same folder
Reference with “../Other_project/”
Git wouldn’t easily download Submodules – SSH certs
External python deps were a separate project

Run AI/ML scripts manually

## Future Plans

Future goal integrate into scrapper to filter
linkedin scrape.
Dev. collab with others.
I attended a talk from someone who presented about their process for automating the application process by documenting where they applied and how they tailored their resume.
I intend to contact them about integrating some of their processes to extend the scope of the toolset I've made.

## Git Repo's

Public repositories

Scraper repo is kept private I'm happy to share it with anyome.
Just assume it would be against somebodys terms of service.
