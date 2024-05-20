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

## Job Search Problems (B.S.) to Automate

I've always found the job search process to be terible, quite simply I'm a programer not a marketer.
It's emotionally draining and I’d rather be programming “Hello World!”
Requires consistency
Broken-promoted job posts
Like Lottery: (with the current market saturation)
“I’ve got about the same chance of winning by not playing.
Goal: anything to improve upon this B.S. so start some automation.

## Tools For The Job

Home NAS
A Jenkins instance
A MongoDB instance
Network+HTTP familiarity
JS experience
Cheerio server-side JQuery for lightweight-headless parsing of HTML
Goal AI/ML automated ranking for filtering out to get best fit jobs

## Development Process

First step was to find where to scrape. Linkedin and others may have an abundance of posts but if they are of low quality without any good filtering and will push out promoted posts before relevant jobs. I found the right site in builtin, with solid search results & programmer specific filters they made searching for tech jobs look easy.

### Dev. Environment

.mjs (node: ECMAScript Module), file of search queries
Save response locally from searches & job
Rate limit queries & Consistent ‘console.log()’

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
