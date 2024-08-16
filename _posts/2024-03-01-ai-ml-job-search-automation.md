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

Find the data: REST-API, HTML [ID, Class, relative]
Found a hidden json blob `<script type=”json”>` with a cache of data not visible elsewhere.
Tree Traversal for relative data
From element with ID main we walk through the child divs
Async, Await, promise timeouts
DRY: Check ID don’t re-scrape posts
.text() & .remove()
Grab the job quick overview-summary text & cleanup
Glean Data
‘/\d._year|year._\d/’ looks for bullet points asking for n years of experience
Test env. Differences:
ex. IDE auto-format modify example response. Will add spaces where web server will compact.
Removed non-existent indentation but block text

## Hide With Browser Headers & Rate Limiting

Automated, use large wait times
Multiple sources (RIP googleWebCache)
be nice to the internet archive
If blocked don’t retry shift source
Cloudflare DNS level bot blocking-detection
Match browser request headers as best bet

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
