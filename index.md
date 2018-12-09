---
layout: default
title: "Build rich live-updating web apps in pure server-side Kotlin"
---

#### Why another web framework, aren't there already too many to choose from?

Let's say you're fairly comfortable with the Kotlin/Java ecosystem, you need to build a rich web application that looks like it belongs in 2018, but you really don't feel like grappling with the massive and complex JavaScript ecosystem.

I've seen this too many times, and so I decided to fix it, and Kweb is the result of that ongoing endeavor.

#### So what is Kweb?

Kweb is a library for building web applications in the [Kotlin](http://kotlinlang.org/) programming language that takes quite a unique approach.

Kweb allows you to interact to the browser DOM directly as if it was local to the web server.  This process is efficient, minimizing browser-server chatter and browser rendering overhead.  DOM fragments are also cached in the browser for an extremely responsive UI, and events are conveyed seamlessly between client and server to maintain consistent state across both.

In this simple example we attach a click listener to a hyperlink:

```kotlin
import io.kweb.Kweb
import io.kweb.dom.element.creation.tags.a
import io.kweb.dom.element.events.on
import io.kweb.dom.element.new
import io.kweb.state.KVar

fun main(args : Array<String>) {

    val globalCounter = KVar(0)

    Kweb(port = 8091) {
        doc.body.new {
            a().text(globalCounter.map { "I've been clicked $it times." }).on.click {
                globalCounter.value++
            }
        }
    }
}
```

The really cool thing here is that the counter is truly global and any change is automatically reflected in the user's browser:

![screencast](https://ucd85dd240dd59417cd50b8be5c5.previews.dropboxusercontent.com/p/orig/AAQXuHUmZuf9CYwe_s_gaZyo9bdhUgp29GAObXxmgWNVu0EBtI4JOcSaSIp88Sr-VnAL_6THqA4AqSZ516_zMhSAdUGPa3DfyuWmU6m1Za6tNMSsQfrh6JBJkU5xwvYniix9cSef9Kk5uZBUBa63SNPulK12oIMh70jX0b8S3kaxYM84Gmyw2Lg7ysQ6ordoUTKwhv6hGMpfExMXORzl5GpT/p.gif?size=1600x1200&size_mode=3)

This is just a simple example, but the paradigm scales up nicely.

#### Kweb's Features

* Build websites in Kotlin
* Makes the barrier between web-browser and web-server largely invisible to the programmer
* Combines surprising convenience for the programmer with efficiency under the hood, minimizing client-server chatter and browser render times
* Seamlessly integrates with powerful JavaScript libraries like [Semantic UI](https://semantic-ui.com/) (for which we have a Kweb plugin offering a fairly comprehensive Semantic UI DSL)
* Bind DOM elements in the browser directly to persistent state on the server and have them update automatically, through the [observer](https://en.wikipedia.org/wiki/Observer_pattern) and [data mapper](https://en.m.wikipedia.org/wiki/Data_mapper_pattern) patterns, using the [Shoebox](https://github.com/kwebio/shoebox) persistent state store.
* Easy to add to an existing project, Kweb is just a library, it doesn't seek to tell you how your project should
  be organized
* Update your web browser instantly in response to code changes


#### How does it work?

Kweb keeps all of the logic server-side, and uses efficient websockets to communicate to web 
browsers. We also take advantage of Kotlin's powerful new coroutines mechanism to efficiently handle
asynchronicity, largly invisibly to the programmer.


#### Can I see an example?

Here is the (heavily commented) main code for a simple to-do list app.  You can find the full app [here](https://github.com/kwebio/core/tree/master/src/main/kotlin/io/kweb/demos/todo):

```kotlin
fun main(args : Array<String>) { 
    /** A simple yet flexible plugin mechanism */
    val plugins = listOf(semanticUIPlugin)

    /** Create a Kweb instance, and configure it to use the Semantic
     * UI framework. Build a simple to-do list app listening on
     * http://localhost:8091/
     * */
    Kweb(port = 8091, debug = true, plugins = plugins) {
        doc.body.new {

            /** Kweb allows you to modularize your code however suits your needs
                best.  Here I use an extension function defined elsewhere to
                draw some common outer page DOM elements */
            pageBorderAndTitle("Todo List") {

                /** A KVar is similar to an AtomicReference in the standard Java
                    Library, but which supports the observer pattern and `map`
                    semantics.  Here I set it to the current URL of the page.

                    This will update automatically if the page's URL changes, and 
                    if it is modified, the page's URL will change and the DOM will
                    re-render _without_ a page reload.  Yes, seriously. */
                val url: KVar<URL> = doc.receiver.url(simpleUrlParser)

                /** s.content uses the semanticUIPlugin to use the excellent
                    Semantic UI framework, included as a plugin above, and implemented
                    as a convenient DSL within Kweb */
                div(s.content).new {

                    /** Note how url.path[0] is itself a KVar.  Changes to firstPathElement
                        will automatically propagate _bi-directionally_ with `url`.  This
                        comes in very handy later. */
                    val firstPathElement: KVar<String> = url.path[0]

                    /** Renders `firstPathElement`, but - and here's the fun part - will
                        automatically re-render if firstPathElement changes.  This is
                        a simple, elegant, and yet powerful routing mechanism. */
                    render(firstPathElement) { entityType ->
                        when (entityType) {
                            ROOT_PATH -> {
                                val newListId = createNewList()
                                url.path.value = listOf("lists", newListId)
                            }
                            "lists" -> {
                                /** Renders can be nested, which means that only this
                                    specific part of the page must be re-rendered if
                                    url.path[1] changes, which is very convenient
                                    for the developer in comparison to other frameworks,
                                    while minimizing server-browser chatter. */
                                render(url.path[1]) { listId ->
                                    try {
                                        /** Here I use the same render mechanism to tie DOM
                                            state to persistent state stored in Shoebox,
                                            Kweb's simple but powerful key-value store with
                                            observer pattern support.  */
                                        renderList(toVar(State.lists, listId))
                                    } catch (e: NoSuchElementException) {
                                        throw NotFoundException("Can't find list with id $listId")
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
```
**Next: [Setting Up]({{ site.baseurl }}{% post_url 2017-03-03-getting-started %}) >>>>**
