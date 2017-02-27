---
layout: post
title:  "1 Golang: mandatory Hello World on Mac OS"
date:   2017-02-12 19:26:32 -0500
categories: jekyll update
---

* Open the terminal, create a directory to put all the demos for this tutorial.
{% highlight bash%}
mkdir golangdemos
{% endhighlight %}
* Create a file called hello.go.
{% highlight bash%}
nano hello.go
{% endhighlight %}
Contents of hello.go:
{% highlight golang %}
package main
import "fmt"
func main() {
    fmt.Println("hello world")
}
{% endhighlight %}
* Then run

{% highlight bash%}
go run hello.go
{% endhighlight %}

