---
layout: default
title: "Build rich live-updating web apps in pure server-side Kotlin"
---

#### News

**19th December, 2018:** Started work on [docs.kweb.io](https://docs.kweb.io/) - which will be the main source of documentation for Kweb.  Please check it out, feedback welcome.

**11th December, 2018:** Received some [attention](https://www.reddit.com/r/webdev/comments/a54i7u/hacker_news_discussion_kweb_a_new_approach_to/) on the webdev subreddit.

**9th December, 2018:** Received some [attention](https://news.ycombinator.com/item?id=18638430) on HackerNews.

**8th December, 2018:** Received some [attention](https://www.reddit.com/r/programming/comments/a4dtp2/kweb_a_new_approach_to_building_rich_webapps_in/) on the programming subreddit.

#### What is Kweb?

Kweb is a library for building web applications in the [Kotlin](http://kotlinlang.org/) programming language, that virtually eliminates the separation between browser and server from the programmer's perspective.

Kweb allows you to interact to the browser DOM directly as if it was local to the web server.  This process is efficient, minimizing browser-server chatter and browser rendering overhead.  DOM fragments are also cached in the browser for an extremely responsive UI, and events are conveyed seamlessly between client and server to maintain consistent state across both.

In this simple example we attach a click listener to a hyperlink which increments a global click counter.  Note how the counter is truly global, and changes to it propagate *automatically* to browsers:

<img src="http://kweb.io/assets/screencast-1.gif" width="100%" />

This is just a simple example, but the paradigm scales up nicely.

#### Kweb's Features

* Build websites in Kotlin
* Makes the barrier between web-browser and web-server largely invisible to the programmer
* Combines surprising convenience for the programmer with efficiency under the hood, minimizing client-server chatter and browser render times
* Easily integrates with powerful JavaScript libraries like [Semantic UI](https://semantic-ui.com/) (for which we have a Kweb plugin offering a fairly comprehensive Semantic UI DSL)
* Bind DOM elements in the browser directly to state on the server and have them update automatically, through the [observer](https://en.wikipedia.org/wiki/Observer_pattern) and [data mapper](https://en.m.wikipedia.org/wiki/Data_mapper_pattern) patterns
* Easy to add to an existing project, Kweb is just a library, it doesn't seek to tell you how your project should
  be organized
* Update your web browser instantly in response to code changes


#### How does it work?

Kweb keeps all of the logic server-side, and uses efficient websockets to communicate to web 
browsers. We also take advantage of Kotlin's powerful new coroutines mechanism to efficiently handle
asynchronicity, largly invisibly to the programmer.


#### Can I see an example?

Yes, [here is a simple TODO list](https://github.com/kwebio/core/tree/master/src/main/kotlin/io/kweb/demos/todo).

**Next: [Setting Up]({{ site.baseurl }}{% post_url 2017-03-03-getting-started %}) >>>>**
