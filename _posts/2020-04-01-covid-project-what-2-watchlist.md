---
title: Covid Project - What 2 Watchlist
author: david
date: 2020-04-01 00:00:00 -0500
last_modified_at: 2024-04-13 00:00:00 -0500
categories: [Problem Solving, Streaming]
tags: [streaming, watchlist, web development, save money, video guide]
pin: true
image:
  path: /assets/img/W2W-Home.jpg
  alt: What 2 Watchlist Homepage.
---

## Project Motivation: Navigating The Streaming Explosion

As we unwind at the end of each day, the promise of streaming services like Netflix beckons us with a vast array of entertainment options. Yet, beneath the surface, lies a frustration familiar to many of us streaming connoisseurs. Browsing from one category to the next with the same movies showing up again and again. Thus you realize just how limited of a library your looking at. It's a scenario not confined to one platform but pervasive across the streaming landscape.

The algorithms meant to assist us in our quest for the perfect show or movie often fall short, offering only a narrow selection of recommendations that fail to capture the breadth of available content. While undoubtedly helpful in surfacing relevant options, these algorithms sometimes serve as a barrier, obscuring the true extent of the library's offerings.

I've found myself countless times resigned to choosing from the limited suggestions, even when I knew there must be more out there, somewhere in the haystack.

## Building a Better Way

In a world where streaming services are abundant, yet content discovery remains a challenge, I embarked on a mission to reshape the way we engage with our entertainment choices. It dawned on me that empowering consumer choice was the key to unlocking the abundance of good content available for streaming. No longer would we settle for the limited offerings presented by a single platform's recommendation algorithms; instead, we would harness the collective power of all available platforms.

Enter What 2 Watchlist: a revolutionary tool designed to transcend the limitations of individual services. With What 2 Watchlist, users can easily curate their personalized libraries by quickly grabbing some of the best blockbusters and finding those forgotten gems that are now streaming for free. We’re empowering users to take control of their entertainment choices. No longer constrained by algorithmic biases or platform exclusivity, viewers can explore the streaming world based on what they Want 2 Watchlist.

## My Vision - Project Goals

- [x] Search and view media details to quickly find recent blockbusters and timeless classics.
- [x] Allow users to create personalized watchlists, ensuring easy access to their favorite content.
- [x] Browse which streaming platforms offer the media on their watchlist, allowing for informed subscription decisions.
- [x] Compare and rotate through streaming services to optimize content availability and variety.
- [ ] Implement privacy.com-like virtual cards to automate subscription management to automatically subscribe to the best content libraries.
- [x] Bonus feature: Match watchlists for movie nights with friends, fostering shared viewing experiences and enhancing social connections.

Join me on this journey as we redefine the streaming experience and empower ourselves to take control of our entertainment choices. While existing services provide a content firehose for endless browsing, What 2 Watchlist offers a curated solution tailored to each user's preferences and needs.

# Development Journey

## TLDR;

Discover the journey behind What 2 Watchlist by exploring its features firsthand. Visit [What 2 Watchlist](https://what2watchlist.com) now!

Watch a comprehensive feature overview on my [YouTube Playlist](https://www.youtube.com/watch?v=htQgphI_Zpc&list=PL_w39zPxLArXwG_s3gm_6X4PoheYBw3TT&pp=iAQB)

{% include embed/youtube.html id='htQgphI_Zpc' %}

## Finding The Right API

The first step on the journey to build What 2 Watchlist began with an exploration of the various API options available. There’s a vast amount of movie websites but only a handful of providers for the type of data I was looking for.

Possible API Sources

- Reelgood
- just watch
- TV Guide
- IMDB
- The Open Movie Database
- TMDB

While all of them had the basics, I needed something that had data on streaming availability and a publicly accessible API.

I dived right into development utilizing Reelgood as my primary API source.
Their comprehensive TV and movie data collection included streaming data.
It was everything I’d need but access to their API had been discontinued for public access.
Undeterred, I delved into their well-designed REST API, leveraging developer tools to peek into HTTP requests to understand their API interface.
This allowed me to play around and get a basic prototype running with search, browsing streaming services, and a lobby system.
I knew this would have broken some terms and conditions to publish an app that was piggybacking off of someone else’s service without permission.

Now that I knew my goals for the app were possible, I’d need to find the same data in a publicly accessible and sustainable API before I could publish the app. Enter TMDB, walking in as the good guys, with overall generous terms and a flexible free-use option. While they were initially lacking streaming data, TMDB later partnered with Just Watch as a data source for integrating streaming availability into their API.

## App Development

Embarking on any project requires a thoughtful selection of tools and frameworks tailored to the task at hand.
As a solo developer, the efficiency and effectiveness of these choices significantly influence the project's trajectory.
For me, prioritizing tools that facilitate rapid development without compromising functionality was essential, allowing me to focus on actual implementation rather than getting bogged down in tedious tasks.

### The Base Tool-Set

In pursuit of these goals, I turned to the familiar landscape of the npm and Node.js ecosystem. Harnessing my familiarity with JavaScript, I initially opted for Meteor.js, a versatile framework renowned for its expediency in crafting Progressive Web Applications (PWAs).
Meteor.js offered an integrated environment that streamlined development, boasting a publish-subscribe model that seamlessly bridged the gap between client and server.
With its robust user-accounts package and JavaScript-centric architecture, Meteor.js provided a solid foundation for realizing the vision of What 2 Watchlist.

Utilizing Meteor.js and a reliable data source from Reelgood, I swiftly laid the groundwork for the application's core functionalities. Within a short span, users could join the platform, create personalized watchlists, and seamlessly browse streaming availability. An example of what can be achieved when proficiency meets powerful tools.

### Going From Prototype To Published

However, as the project progressed, the need for a more robust and compliant API became apparent. Transitioning to TMDB not only ensured adherence to legal standards but had also broadened the application's functionality. With TMDB having similar but well documented data, I now had easy access to the global availability of streaming data. Now users could browse through the global market for streaming content. Know not just what was streaming but where it was streaming. The integration of TMDB represented a significant milestone, unlocking new possibilities and broadening the app's appeal.

#### Bonus Lobby

With the core functionalities of the application established, attention turned towards more complex functionalities.
Among these was the development of a movie night lobby, designed to facilitate collaborative movie selection among friends.
This bonus feature took priority, the other features had some liability concerns I’d need to address before publishing.
Despite its seemingly straightforward premise, the implementation posed several challenges.
While Meteor has many built in features to sync data from the server a lobby requires some transactional synchronization on the server.
I had to make sure the backend server code was being executed asynchronously and accurately relaying if the lobby is waiting for people to join or already in progress.
Nonetheless, perseverance, a whiteboard and innovative (hacky) problem-solving ultimately led to the successful realization of this feature, but the overall feature complexity would have me revisiting the code base again and again later on down the line.
Overall this gave users a great way to engage with the app by quickly comparing movie interests with friends.

#### The Road To Virtual Cards

As the project approached its final stages, attention turned towards deployment readiness and the potential/hurdles of monetization through virtual card features.
After sharing the app with a select group and doing some research, I discovered Privacy.com was rebranding to Lithic.
Expanding their platform to give businesses the abilities for virtual card issuance and management services.

This was precisely the service needed to implement virtual cards for automating subscription management.
Recognizing this as potentially the killer feature of this app, development efforts swiftly began on this feature.
Utilizing Lithic's sandbox environment, initial steps were taken to establish user sign-up processes, and explore API calls for the card management functionalities.

However, before fully incorporating virtual card functionality, it was essential to establish a robust system for tracking and managing user subscriptions.
Envisioning the manager acting as a monthly value checkup where users would chose their best subscription options.
Thus, focus shifted towards facilitating informed decision-making on streaming services.
This involved curating a selection of popular streaming services, gathering data on pricing and available plans, and developing tools for comparing and evaluating each service's offerings.

The ultimate goal was to empower users to effortlessly manage and optimize their subscription choices based on their individual viewing preferences.
With the groundwork laid for subscription management services, the path towards integrating Lithic's virtual card API for seamlessly subscription hopping could now start to crystallize.

The pursuit of virtual card integration and subscription management presented a sizable amount of work that was a few layers in, making it likely to only be used by power users.
Considering the liability concerns around handling other peoples money this did not reach a completion stage, but a proof of concept stage.
I'd gone and implemented the majority of features needed just to verify that it was possible.

Despite the promise and potential of virtual card integration, the endeavor presented a measurable amount of work and complexity.
Liability concerns surrounding handling other people's financial transactions added an additional layer of scrutiny.
As a result, while a proof of concept was achieved, reaching a full implementation stage remained a work in progress.
Nonetheless, there is still a sense of excitement surrounding this feature, leaving open the possibility of revisiting it to enhance the user experience and monetize this project.

#### Revamping UI Experience

Moving beyond functionality, my attention now directed towards enhancing the application's user interface and experience.
Recognizing the lack of visual appeal in a white background and interactive text, I would need a complete UI overhaul.
I evaluated various UI frameworks and ultimately chose Semantic-UI for its strong documentation + examples, a sleek UI, with a full suite of customizable components and most importantly it was free.

After seeing all that Semantic-UI had to offer I had a clear picture of where and how I could use its components to improve the user experience.
The meticulous process of redesigning and refining each page yielded a transformational result, elevating the overall aesthetic and usability of the application.
Semantic-UI proved to be a strong library where the developer experience was as good or better than most of my previous experiences; where I’d just throw bootstrap classes at the project and call it a day.

#### Security

With the majority of milestones achieved and the app nearing readiness for public release, emphasis was placed on fortifying its security and accessibility.
Measures such as user rate limiting, HTTP header permissions, and server hardening were implemented to safeguard user data and mitigate potential vulnerabilities.
Furthermore, the adoption of HTTPS encryption via Nginx and Let's Encrypt ensured secure communication channels, reinforcing user trust and confidence in the platform.

#### Publish

As the journey of prototype to publication draws to a close, What2Watchlist became publicly accessible.
With all project goals accomplished, except for virtual card integration,
(The potential liability of this feature was too high for something that’s currently just a portfolio project.)
Nonetheless, I am truly satisfied by what I have accomplished.

As a portfolio project it showcases the growth I have experienced embarking on a meaningful solo development project.
The project represents the culmination of months of hard work overcoming various challenges and learning valuable lessons along the way.
The progress made thus far stands as a testament to the power that all software developers are capable of harnessing through perseverance and foundational problem-solving skills.

### Re-Evalueate Re-Factor

After completing my sprint to get What 2 Watchlist hosted publicly, all the tasks sitting on the back-burner could now be addressed.
I recognized several quality-of-life improvements and technical changes that could enhance the project overall.
One of the foremost changes involved transitioning from the Meteor frontend to Vue.js.
The default frontend framework in Meteor, Blaze, left me frustrated due to its lack of reactivity, leading to excessive boilerplate code that became tedious to manage.

Upon evaluating reactive frameworks, I compared Vue.js and React.js.
Vue's familiar syntax and straightforward component framework resonated with me, especially when compared to React's more unstructured design.
Vue's concise tutorials and shorter learning curve ultimately solidified my decision to adopt it over React, despite React's broader industry adoption.

However, transitioning to Vue was not without its challenges.
I encountered dependency errors that required thorough investigations to resolve, as documentation for Meteor and Vue integration was limited.
Showing just how important having a large community is when encountering development hurdles.
Despite any regrets about not choosing React, I persisted and successfully ported the UI to Vue components.

In this process, I discovered the Element Plus UI library tailored specifically for Vue.
Its extensive collection of UI components, along with customizable hooks for seamless integration, proved invaluable in enhancing the app's visual appeal and functionality.

Modifying complex sections such as the lobby and decoupling the default account sign-in page posed significant challenges.
While the lobby's intricate code necessitated careful evaluation with each modification, the sign-in page at least had an MIT-licensed example project to use as a starting point.

#### Quality of life improvements

- Popular titles
  - Curated list of blockbusters
  - For each year since 2005
- UI cleanup
  - Unappealing card borders removed
  - Color icons for Add-remove action
  - Icons: for navigation, help and highlighting things.
  - Anything else with rough edges
- Improved media details for where to find media streaming.
- Streaming manager drawer
  - To easily hide or access of the streaming services you’re interested in comparing.
- Element UI tables
  - Sort streaming service metrics based on user choice.
- Watchlist creation without creating an account
- porting some UI elements to the new library for code consistency
- backend unit tests.

As I continued refining the app, I began reassessing certain components of Meteor, gradually reducing reliance on the platform in favor of tools with larger communities.
I found myself navigating through dependency hell one too many times, encountering libraries that were broken in the latest versions or had conflicting dependencies.
All of these factors can be frustrating, especially when you're eager to dive into coding and move the project forward.

Then, Vue 3 emerged onto the scene, prompting me to explore its capabilities. In the spirit of learning, I decided to port the UI once again.
However, another code rewrite shed light on the possibility that React might have been a more suitable choice in the long run.
With its seamless development experience, longstanding development paradigm, and widespread industry adoption, React appears like it would have offered a smoother path forward compared to Vue.
Nevertheless, this project serves as a valuable learning experience, allowing me to compare and contrast the trade-offs between these two technologies.

## Development blocks and push through to publish

### Initial Project Rush

The inception of this project coincided with a surge of motivation spurred by the initial onset of the Covid-19 pandemic.
It was a time ripe for focused work, with the opportunity to channel my energy into a project that deeply motivated me.
Progress was swift, and despite initial concerns about utilizing the Reelgood API, the allure of creating a compelling portfolio piece and the sheer enjoyment of the development process carried me forward.

### Covid-19 Pandemic Struggles

Regrettably, the initial burst of productivity was short-lived.
As my roommates and I reached the end of our lease, we dispersed to stay with family members, disrupting my development momentum.
While I'm grateful for my family's support, adjusting to a different environment, marked by what I affectionately term "retirement decline," posed its challenges.
The perpetual background noise of low quality TV speakers blaring CNN, made it challenging to concentrate on programming.
Consequently, months stretched into nearly a year before I could muster the determination to resume work on the project.
Nevertheless, this hiatus wasn't entirely unproductive, as I undertook several side projects to gradually regain momentum.

<h4>Side Projects</h4>

- Constructing a desk
- Converting a bike into an e-bike and designing/building a battery
- Upgrading a 3D printer
- Developing a 3D-printed tool for putting on dog booties
- Creating 3D-printed arch supports using photogrammetry
- Installed Home Assistant on my NAS for automated lighting

External concerns also contributed to development pauses, with access to a publicly available API and liability issues surrounding virtual card management services being significant hurdles.
These interruptions, though lengthy, often faded from memory during periods of productivity, only to resurface as familiar roadblocks.
Fortunately, breakthroughs came with the expansion of TMDB's API to include streaming data and the emergence of Lithic's public API for virtual card management, providing renewed hope for the project's fruition.
However, the realization that certain Lithic features were restricted to enterprise customers was a setback, dampening my hopes to reach feature completion but not extinguishing my development enthusiasm.

### Pushing Through

Participating in a Devember coding event hosted by the Level1Tech forum injected a shot of adrenaline into the project.
The camaraderie and accountability fostered in this environment propelled me forward, enabling me to surmount my anxieties and focus on delivering tangible results.
The culmination of this effort was a publicly accessible site of which I am immensely proud, serving as a testament to the power of perseverance and determination.

Post-launch, my enthusiasm remained undiminished, fueling efforts to enhance the project further.
I embarked on a journey to learn about reactive UI frameworks, driven by a desire to leverage whats become the new common industry tool-set to elevate the project's quality.
The accolades received, including recognition from Wendell at Level1Techs, provided validation for the hard work invested in the project.
However, the realization dawned that sustained motivation requires long-term results, a domain where my strengths as a developer may not necessarily align with the marketing acumen required to grow the app's user base.

### Reception

Despite efforts to share the app with different groups or create video tutorials showcasing What2Watchlist's features and benefits, user acquisition remained elusive.
Many people who looked at my app would say, “oh, that's really cool,” but it never translated into them trying it and providing feedback.
Being privacy-minded, I refrain from injecting any app tracking to monitor user behavior, making it difficult to gauge user engagement accurately.
It's uncertain if any users other than myself exist, but if any do, they seem to engage only at the surface level and are not motivated enough to seek added benefits with an account.
Being in a position where I want my efforts to be recognized, ongoing development has been halted until I can attract enough site traffic to drive user sign-ups, necessitating a shift towards basic site maintenance.

### Lessons Learned

In retrospect, while there are lessons learned and areas for improvement, this project has been invaluable in fostering personal growth and resilience.
It serves as a testament to the importance of dedication and perseverance, demonstrating that if you believe in something, go reach for it;
with perseverance, you'll find yourself reaching farther than you thought you could have.
This underscores the importance of stepping beyond one's comfort zone while also acknowledging the limitations of certain skill sets that may not be fully developed.
While this project was a solo endeavor, the challenges of the Covid-19 pandemic highlighted how the social nature of teamwork breaks down many project roadblocks.
During solo development, catching the right flow state was like lightning in a bottle, powerful but unable to guide itself perpetually.
Being social creatures, it's essential to find the right team where you can lean on others, bounce ideas, and collectively create a shared vision.
ChatGPT is a helpful tool, but it cannot replace the wisdom and synergy that come from a well-functioning team.

# Feature Highlights

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

# future plans

## tentative plans for future development

Hoping to create a companion app to manage users virtual cards locally.
The blocks are knowing that I don't currently have an active user-base other than myself.

There is a lot more that could be tracked for TV shows many services are only offering the first episode of a season free of have a handful of episodes from a show.
I'd like to be able to better convey this.
currently the API for streaming data is just a yes or no there is no granularity.
so people might be mislead by a show saying it is available for free or on a streaming service.

These are all things I need to be motivated to to and until I have actual users I'm just making this for myself.
Currently it is working good enough for me.

# Conclusion
