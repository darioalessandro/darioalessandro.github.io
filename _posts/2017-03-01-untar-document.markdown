---
layout: post
title:  "5: Untar file"
date:   2017-03-01 10:00:00 -0500
categories: tar linux mac tgz .tar.gz
---

If Your File Extension is .tar.gz (or .tgz)

If your tar file is compressed using a gZip compressor, use this command:

1 tar xvzf file.tar.gz

The options are pretty straightforward for this:

x: This tells tar to extract the files.

v: This option will list all of the files one by one in the archive. The “v” stands for “verbose.”

z: The z option is very important and tells the tar command to uncompress the file (gzip).

f: This options tells tar that you are going to give it a file name to work with.


