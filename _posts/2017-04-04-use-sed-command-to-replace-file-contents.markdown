---
layout: post
title:  "8. Use sed command to replace file contents"
date:   2017-04-04 10:00:00 -0500
categories:  programming google terminal general motors unix linux java devops
---

## In place text replacement:

```shell
sed -i '' 's/oldword/newword/' <file>.txt
```

## Practical usecase: Change default memory of Zookeeper.

```shell
sed -i '' 's/KAFKA_HEAP_OPTS="-Xmx512M -Xms512M"/KAFKA_HEAP_OPTS="-Xmx256M -Xms256M"/' zookeeper-server-start.sh
```

Will replace the KAFKA_HEAP_OPTS initial values from "-Xmx512M -Xms512M" to "-Xmx256M -Xms256M".

