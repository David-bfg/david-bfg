---
title: Covid Project What 2 Watchlist
author: david
date: 2020-04-01 00:00:00 -0500
last_modified_at: 2024-03-03 00:00:00 -0500
categories: [Problem Solving, Streaming]
tags: [streaming, watchlist, web development, save money, video guide]
pin: true
image:
  path: /assets/img/W2W-Home.jpg
  alt: What 2 Watchlist Homepage.
---

## Project Motivation

Ending each day relaxing and turning on Netflix can make you keenly aware of how limited of a library they actually have. & others stuck on a limited content library. The recommendation algorithm: it does not matter how big the library is if the recommendation algorithm keeps recommending only a handful of movies that it thinks you may like

## TLDR;

Find out about this project by just using it. [What 2 Watchlist](https://what2watchlist.com) is live and available for general use. Go check it out to learn more.

Watch my [YouTube Playlist for a feature overview](https://www.youtube.com/watch?v=htQgphI_Zpc&list=PL_w39zPxLArXwG_s3gm_6X4PoheYBw3TT&pp=iAQB)

{% include embed/youtube.html id='htQgphI_Zpc' %}

## <s>Looking For</s> Building a Better Way

I stopped asking "what’s good on Netflix." Back in the day when you'd get DVDs in the mail, the question was "what's good?"
There is an abundance of great content today, blind trust in your favorite service.
on several different services. How can I get the best content around?

I thought people are looking at this problem wrong it should be “who is streaming what I want to watch.” While there are services out there that look at what’s streaming on a given service they don’t answer my question they just let me browse until I’m blue.

Thus I have been working on a project with the idea to go and build up a watchlist to match against the existing streaming services out there be it something available for free with adds or some type of original content. You can get the most bang for the buck and move on to the next streaming library once you’ve cleared it out.

## Project Goals

- [x] search & view media details
- [x] users can create watchlist
- [x] browse who streams the media on your watchlist
- [x] compare and rotate through streaming services
- [ ] privacy.com like virtual cards to auto swap streaming subscriptions
- [x] Bonus: Match between watchlists for movie night with friends

## Finding an API

- Reelgood
- just watch
- TV Guide
- IMDB
- The Open Movie Database
- TMDB

I began by searching for sources for streaming data and found these providers.
I got started using Reelgood. it had both movie and show basic data along with information about where media was streaming. Unfortunately they did not have a public API but they had a well designed rest API that you could find out how it worked by simply using their website and watching the http requests in the developer console.

allowed me to create some basic pages that I could run locally.
Search, browse streaming services for watchlist matches and a lobby system-feature to find what to watch with friends.

I was aware that this solution was not going to be a long term acceptable use case.
if I was going to publish my app I'd need to use an api that was public facing.
so I was happy to have found that TMDB a publicly available api.
Unfortunately they did not include streaming data initially so I had paused development for a while.
I came back to the project later on and found they partnered with just watch to include streaming data availability for their API.

## App Development

I started off using a tool I was familiar with specifically meteor.js.
It's a framework for quickly making PWAs that was popular when I first got into the Node.js ecosystem.
I began development by creating very simple pages.
meteor has a convenient user accounts package to help jump start creating a new app.
I'd first implement the basic search functionality then allow you to track media by adding it to your users watchlist.
On the backend each movie added to your watchlist was queried for all the streaming services related so it would have that data cached locally.

next steps were to combine the users watchlist with the available streaming data and build a page for browsing where what you want to watch is currently streaming.
the initial implementation had only bothered getting data for what was streaming in the US.
later revisions were using the TMDB API and that would return streaming data for all countries.

with that foundation and backend API streaming data I had the foundation for browsing through a watchlist.
you would start with your full watchlist and filter down by what streaming service you wanted to review how it matches with your interests or just

### search

#### Basic Start

I started with making a simple page with just a search input and a list of results. it was just the basics white background, black text, with an input and button.
the search had no pagination it only responded with the first page of results but did have a quick response/auto-complete that showed quickly before the full page results were requested.

The search initially had a 3 character limit as a hack to implement debounce. I had been annoyed by seeing the same early results flash multiple times. They were not helpful so I found a way to block them. eventually my google-fu found debounce and I got to learn about debounce it.

It was not much but Meteor was feature rich enough that I had a reactive search bar (auto-updated search response.)

#### refined state

Long term I added and learned much more about different UI components.

cards
: to have a template for showing details for media and auto-adjust the list of search results to fit the page width.

Icons, buttons and labels
: added different icons to signal information to the user IE. if a card is a movie, a tv show or an actor. Icon buttons were used to signify if you were adding or removing media from your watchlist.

Media Poster Images
: added poster images to represent a search result. Using CDN from TMDB API for responsiveness and to lessen server load.

### users & Watchlist

used meteor library for accounts to get user functionality setup in app.
now a user can build their watchlist and we will track the streaming data of the media in your watchlist.

worked on some mongo queries to store a watchlist with each user.
I Specifically wanted to store the users watchlist as a set or map data structure where an id lookup would let you know if a movie is in the watchlist.
This was a non-standard use case that required a way to use a dynamic key in the mongo query syntax.
after searching google and reading documentation I came upon this syntax for adding a variable to a query path.

```javascript
{$set: {['userProfile.watchlist.' + id]: imdbNum}}
```

#### refined state

long term I added the ability to build a watchlist locally before you create an account.
this went along with revamping the builtin accounts and making a dedicated page for user sign-up.
You'd have an email attached to your account with all the basic features of password and password recovery.
You'd also have the ability to import your local watchlist to your new account.
along with all this I implemented SMTP (e-mail) on my domain.
I had to chose between rolling my own or finding a good service and from the looks of it you'd go to spam if you weren't using a service.
Eventually I found a quality service that was most importantly free 'Zoho Mail.'

### media details

This stated out as just a page to view the poster and review more data relating to the movie or show.

it Is still simple but has been given a coat of paint (CSS styling) and details about where you can stream the piece of media you're interested in. The streaming is separated by where it is available for free or with a subscription service and in what region that streaming service is offering it.

### watchlist - browsing

transform from basic list of media and radio selector for streaming services to a similarly simple but prettier view for browsing between streaming services.
An option to select the region you are streaming from is the only new functionality.

other than the UI improvements there has been a rework of how media lists are shown.
browsing through a selection of media had been reworked into a reusable component with infinite scroll for auto pagination.

### movie night with lobby

This was a bonus feature that I ended up focusing on earlier than expected.
while I would have liked having privacy.com like feature integration, There's a lot of scary words around liability when you look into APIs with the ability to programmatically managing other peoples payments.
thus being unsure about taking on bank like liability I worked on making a lobby system finding what movie to watch.

The overall idea is that you could easily check between users to find matching media.
so when your buddies are looking to have a movie night you can easily find something everybody is interested in.
The process would start out by creating a lobby and sharing the link with your friends.
once everyone has entered ready up and you will be able to browse through the media matches in your group.
browse based on the number of friends that have matching watchlists.
from that set you will be able to browse in the same manner you do for a watchlist.
Say you're all relying on Jeff's Netflix account you can filter based on the available streaming services.

Functionally this had a lot of complexity added from managing state between multiple users.
I found myself doing a lot of whiteboard work to map out the state machine for how state was syncing between multiple users.
I'd often get to a point where things seemed good hoping I didn't need to touch things again and some rework has me returning reevaluating all my code until I'd end up rewriting a significant portion.
the complexity of managing state between users has a lot of moving parts.
the Meteor framework has a lot of tools for syncing data models to the users view.
That has often been convenient, but I found that the way I though synchronization between users should work and the subscription/publish paradigm used in meteor.
it had me implementing something that was a painful hodgepodge but at least it works and I can finally be done.
that was true always true till the next little rewrite.

### compare and rotate through streaming services

for my plan to implement privacy.com like functionality to help people jump in and out of a subscription quickly by just starting or pausing payments required some system for management.
for that I had created a streaming manager feature.
you'd select the streaming services you'd consider using and manage who you'd be subscribing to from one month to the next.

we show a table of metrics related to the popularity, size and average rating.
with a means to browse through your watchlist matches in each services library.

### virtual cards to auto swap streaming subscriptions

I had created a sandbox feature for allowing users to create virtual cards.
I had found an spin-off or off-shot of privacy.com, lithic is a public API for publishing and managing virtual cards.
I had implemented some of the basic functionality needed for publishing cards in someones else's name.
plus features related to managing and viewing transactions.

overall tested the waters on what would be needed but I am an individual not in the position of acting as a bank.

## development blocks and push through to publish

Initial rush to work on a project I was motivated by was halted by needing to move cross state.
project starts and stops were caused because of external concerns such as getting access to an api that was publicly available, streaming data availability, and how could I actually offer virtual card management to allow users to pause and start services from one management page.

## tentative plans for future development

Hoping to create a companion app to manage users virtual cards locally.
The blocks are knowing that I don't currently have an active user-base other than myself.

There is a lot more that could be tracked for TV shows many services are only offering the first episode of a season free of have a handful of episodes from a show.
I'd like to be able to better convey this.
currently the API for streaming data is just a yes or no there is no granularity.
so people might be mislead by a show saying it is available for free or on a streaming service.

These are all things I need to be motivated to to and until I have actual users I'm just making this for myself.
Currently it is working good enough for me.
