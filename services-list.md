---
title: Services List
layout: paas_cli
---

# Services List

```
Usage: catalyze services list

List all services for your environment
```

`services list` prints out a list of all services in your environment and their sizes. The services will be printed regardless of their currently running state. To see which services are currently running and which are not, use the [status](#status) command. Here is a sample command

```
catalyze services list
```