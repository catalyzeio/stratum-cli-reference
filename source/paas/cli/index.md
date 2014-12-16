---
title: Introduction
layout: guides_paas
---

# Introduction

Usage: `catalyze [OPTIONS] COMMAND [ARGS]...`

Options:

```
  --baas-host TEXT   Alternate BaaS API URL
  --paas-host TEXT   Alternate PaaS API URL
  --skip-validation  Skip certificate validation
  --help             Show this message and exit.
```

Commands:

```
  associate     Associates a local repository with an environment.
  dashboard     Open the Catalyze dashboard in your browser.
  db            Import/export databases schemas and contents.
  disassociate  Remove association with environment.
  environments  Lists all environments you own.
  rake          Execute a rake task.
  redeploy      Redeploy an environment's service manually.
  status        Check the status of the environment and...
  vars          Check/set/unset environment variables.
  version       Outputs the current CLI version.
  worker        Start a background worker.
```