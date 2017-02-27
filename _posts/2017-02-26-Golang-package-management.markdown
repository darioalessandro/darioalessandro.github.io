---
layout: post
title:  "3 Golang: Package Management"
date:   2017-02-26 19:26:32 -0500
categories: golang package management dep
---

# Package Management

<b>Objective: </b> Determine package manager to be used with go projects and walk through the basics.

According to [PackageManagementTools](https://github.com/golang/go/wiki/PackageManagementTools) the official package manager is [dep]( https://github.com/golang/dep )

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

