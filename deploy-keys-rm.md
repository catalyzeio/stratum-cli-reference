---
title: Deploy Keys Rm
layout: paas_cli
---

## Deploy Keys Rm

```
Usage: catalyze deploy-keys rm NAME SERVICE_NAME

Remove a deploy key

Arguments:
  NAME=""           The name of the key to remove
  SERVICE_NAME=""   The name of the code service to remove this deploy key from
```

`deploy-keys rm` will remove a previously created deploy key by name. It is a good idea to rotate deploy keys on a set schedule as they are intended to be shared among an organization. Here are some sample commands

```
catalyze deploy-keys rm app01_public app01
```