---
title: Default
layout: paas_guides
---

# Default

```
Usage: catalyze default ENV_ALIAS

Set the default associated environment

Arguments:
  ENV_ALIAS=""   The alias of an already associated environment to set as the default
```

`default` sets the default environment for all commands without a specified environment and run outside of a git repo. See [scope](https://resources.catalyze.io/paas/cli/sections/global-scope/) for more information on scope and default environments. When setting a default environment, you must give the alias of the environment if one was set when it was associated and not the real environment name. Here is a sample command

```
catalyze default prod
```