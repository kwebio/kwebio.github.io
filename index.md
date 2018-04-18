---
layout: default
title: "Introduction to Kweb"
---

1. TOC
{:toc}

#### What is Kweb?

Kweb is a library for building web applications in the [Kotlin](http://kotlinlang.org/) programming language.

Kweb is fairly unusual in that, while your code runs on the server, Kweb allows you to interact to the browser DOM directly as if it was local to the web server. 

For example, here we create a `<p>` element and set its text:

```kotlin
doc.body.p().text("this is an example HTML paragraph")
```

Kweb has plugins for JavaScript libraries like [JQuery](https://jquery.com/) and 
[others](https://github.com/kwebio/core/tree/master/src/main/kotlin/io/kweb/plugins).  It's also 
surprisingly easy to build your own plugins for other JavaScript libraries, or extend those Kweb already
supports.

#### Features
* Build websites in Kotlin
* Makes the barrier between web-browser and web-server largely invisible to the programmer
* Seamlessly integrates with powerful JavaScript libraries like JQuery, Semantic-UI, and others
* Update your web browser instantly in response to code changes
* Bind DOM elements in the browser directly to persistent state on the server and have them update automatically, through the [observer](https://en.wikipedia.org/wiki/Observer_pattern) and [data mapper](https://en.m.wikipedia.org/wiki/Data_mapper_pattern) patterns, using the [Shoebox](https://github.com/kwebio/shoebox) persistent state store.
* Easy to add to an existing project, Kweb is just a library, it doesn't seek to tell you how your project should
  be organized

#### How does it work?
Kweb keeps all of the logic server-side, and uses efficient websockets to communicate to web 
browsers. We also take advantage of Kotlin's powerful new coroutines mechanism to efficiently handle
asynchronicity, largly invisibly to the programmer.

#### What does it look like?

Here is the main code for a simple to-do list app, find the full app [here](https://github.com/kwebio/core/tree/master/src/main/kotlin/io/kweb/demos/todo):

```kotlin
// Starts a web server listening on port 8091
Kweb(port = 8091, debug = true, plugins = listOf(semanticUIPlugin)) {
    doc.body.new {
        div(Style.outerContainer).new {
            div(Style.innerContainer).new {
                route(withGalimatiasUrlParser) { url ->
                    val pageHeading = h1(Style.listHeadingStyle).text("Shopping list")
                    div(semantic.content).new {
                        render(url.path[0]) { entityType ->
                            logger.info("Rendering entity type $entityType")
                            when (entityType) {
                                ROOT_PATH -> {
                                    createNewListAndRedirect(url.path)
                                }
                                "lists" -> {
                                    logger.info("Rendering lists/${url.path[1]}")
                                    render(url.path[1]) { listUid ->
                                        try {
                                            val list = asBindable(State.lists, listUid)
                                            renderList(list)
                                        } catch (e : NoSuchElementException) {
                                            throw NotFoundException("Can't find list with id $listUid")
                                        }
                                    }
                                }
                                else -> {
                                    throw NotFoundException("Unrecognized entity type '$entityType', path: ${url.path.value}")
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}
}
```
**Next: [Setting Up]({{ site.baseurl }}{% post_url 2017-03-03-getting-started %}) >>>>**
