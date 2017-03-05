---
layout: post
title:  "3 Golang: Dependency Management"
date:   2017-02-26 19:26:32 -0500
categories: golang package management dep go golang
---


## Objectives

Determine good options for dependency management tools and walk through the basics.

## Motivation

Managing dependencies is one of the basic building blocks of any decent modern programming language toolchain.

### Java:

For Java we have the next:

<ol>
    <li> <a href="https://maven.apache.org">Maven</a> Dependency Management + Build Tool.</li>
    <li> <a href="http://ant.apache.org/ivy/">Ivy</a> Dependency Management.</li>
    <li> <a href="http://www.scala-sbt.org">SBT</a> Build Tool, it uses Ivy for Dependency Management.</li>
    <li> <a href="https://gradle.org/">Gradle</a> Build Tool and it uses Maven for Dependency Management.</li>
</ol>

All the above are mature tools, with great support and different capabilities, I tend to like sbt + ivy because it is not some crappy scripting thing, it is a full fledged
code driven build tool which allows for max flexibility and customization.

### Go:

Go seems to have a huge vibrant community of devs working in parallel to build the tool chain.

Today we will explore a few ideas around package management and build tools.

## "go get" command

As of today, the official way to get code from a website to your $GOPATH is

```sh
go get
```

I find it very pedestrian, looks like a placeholder that, understandably, the Go team had to have to concentrate on the language.

There's no way (that I am aware of) to specify the version of the dependency that you want, it always pulls the latest.

This makes it really hard to create reproducible builds.

One good thing is that it is capable of pulling code from git repos.

## GoDep

** (Excerpt From: Sau Sheong Chang. “Go Web Programming.”)

Heroku uses godep (https://github.com/tools/godep) to manage Go dependencies. To install godep, use the standard go get command:

```sh
 go get github.com/tools/godep
```

Once godep is installed, you need to use it to bring in your dependencies. In the root directory of your web service, run this command:

```sh
godep save
```

This command will create a directory named Godeps, retrieve all the dependencies in your code, and copy their source code into the directory Godeps/_workspace.

It will also create a file named Godeps.json that lists all your dependencies. This listing shows an example of the Godeps.json file.


```javascript
{
  "ImportPath": "github.com/sausheong/ws-h",
  "GoVersion": "go1.4.2",
  "Deps": [
    {
      "ImportPath": "github.com/lib/pq",
      "Comment": "go1.0-cutoff-31-ga33d605",
      "Rev": "a33d6053e025943d5dc89dfa1f35fe5500618df7"
    }
  ]
}
```
To learn more about godep, please go to the [godep github site](https://github.com/tools/godep).

## Dep

According to [PackageManagementTools](https://github.com/golang/go/wiki/PackageManagementTools) the official package manager is [dep]( https://github.com/golang/dep).

<b>Warning!!!:</b> One big bummer is that even when the Go team says that dep is the official dependency manager, you will notice that the dep page states that:

> Dep is a prototype dependency management tool. It requires Go 1.7 or newer to compile.

> Dep is NOT an official tool. It is not (yet) blessed by the Go team.

> It IS, however, the consensus effort of most of the Go community, and being integrated into the `go` toolchain is the goal.

> Pre-alpha. Lots of functionality is knowingly missing or broken. The repository is open to solicit feedback and contributions from the community.
Please see below for feedback and contribution guidelines.

### Usage (taken from [dep]( https://github.com/golang/dep) )

Get dep tool via

```sh
$ go get -u github.com/golang/dep/...
```

Typical usage on a new repo might be

```sh
$ dep init
$ dep ensure -update
```

To update a dependency to a new version, you might run

```sh
$ dep ensure github.com/gorilla/websocket@^1.1.0
```

Then run

```sh
$ dep ensure
```

To make sure that dep downloaded the package, run:

```sh
dep status
PROJECT                       CONSTRAINT  VERSION  REVISION  LATEST  PKGS USED
github.com/gorilla/websocket  ^1.1.0      v1.1.0   3ab3a8b   v1.1.0  1
```

# Conclusion

Seems like godep and dep are two real options to use, I will try to use dep and I'll share my experiences through this blog.
