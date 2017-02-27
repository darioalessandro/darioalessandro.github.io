---
layout: post
title:  "3 Golang: Package Management"
date:   2017-02-26 19:26:32 -0500
categories: golang package management dep
---

# Go Package Management

<b>Objective: </b> Determine package manager to be used with go projects and walk through the basics.

According to [PackageManagementTools](https://github.com/golang/go/wiki/PackageManagementTools) the official package manager is [dep]( https://github.com/golang/dep).

## Warning:

One big bummer is that even when the Go team says that dep is the official package manager, you will notice that the dep page states that:

Dep is a prototype dependency management tool. It requires Go 1.7 or newer to compile.

### Dep is NOT an official tool. It is not (yet) blessed by the Go team.

It IS, however, the consensus effort of most of the Go community, and being integrated into the `go` toolchain is the goal.

## Usage

Get dep tool via

```sh
$ go get -u github.com/golang/dep/...
```

Typical usage on a new repo might be

```sh
$ dep init
$ dep ensure -update
```

