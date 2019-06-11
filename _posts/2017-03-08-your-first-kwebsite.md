---
layout: page
title: "Your first kwebsite"
category: use
order: 2
date: 2017-03-08 10:34:51
---

### This page is obsolete, please refer to https://docs.kweb.io/

Add a file like the following to your project:

```kotlin
fun main(args: Array<String>) {
    Kweb(port = 8080) {
        doc.body.new {
            h1().text("Hello World!")
        }
    }
}
```

Run the file and visit [http://localhost:8080/](http://localhost:8080/) in your web browser, ta-da!

Notice how a lambda is passed to the creation method of the body, **doc.body.new {...}**. These creation methods enable the use of nested DSL-style syntax. We could have written the body `doc.body.new().h1.text("Hello World!")` and the result would be the same.

### A more ambitious example

Let's edit the file to do something more interesting:

```kotlin
fun main(args: Array<String>) {
    var count = 0

    Kweb(port = 8080) {
        doc.body.new {
            val output = h1().text("Count: " + count)
            button().text("Click me!").on.click {
                output.text("Count: ${++count}")
            }
        }
    }
}
```

Here we create a header-1 element and assign it the to variable **output**. We then create a button to increament our count. Finally we add a click listener to the button to increment the count and update the output text.

Kill Kweb if it is still running, and run this new version.  Click the button and notice how the count .

**Troubleshooting**: If you get an error like `Exception in thread "main" java.net.BindException: Address already in use` it means 
that the previous version is still running and therefore the new version is unable to listen on port 8080, make
sure you've killed it.

-----------
**Next: [Live coding]({{ site.baseurl }}{% post_url 2017-03-09-live-coding %}) >>>>**
