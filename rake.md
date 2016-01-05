---
title: Rake
layout: paas_guides
---

# Rake

```
Usage: catalyze rake TASK_NAME

Execute a rake task

Arguments:
  TASK_NAME=""   The name of the rake task to run
```

`rake` executes a rake task by its name asynchronously. Once executed, the output of the task can be seen through your logging Dashboard or using the [logs](https://resources.catalyze.io/paas/cli/sections/logs/) command. Here is a sample command

```
catalyze rake db:migrate
```