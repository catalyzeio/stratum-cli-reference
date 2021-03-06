# Automatic Updates

Any version of the CLI before version 4.0.0 will no longer automatically update. In order to obtain version 4.0.0 you will have to manually install the CLI. Automatic updates will work again once you have upgraded to 4.0.0 or greater.

Once downloaded, the CLI will attempt to automatically update itself when a new version becomes available. This ensures you are always running a compatible version of the Datica CLI. However you can always check out the latest releases on the [releases page](https://github.com/daticahealth/cli/releases).

To ensure your CLI can automatically update itself, be sure to put the binary in a location where you have **write access** without the need for sudo or escalated privileges.

# Supported Platforms

Since version 2.0.0, the following platforms and architectures are supported by the Datica CLI.

| OS | Architecture |
|----|--------------|
| Darwin (Mac OS X) | 64-bit |
| Linux | 64-bit |
| Windows | 64-bit |

# Global Scope

Datica CLI commands can be run anywhere on your system with two exceptions. The [`datica init`](#init) command expects to be inside of either a git repository or a directory that you intend to be a git repository, as it will set up a git repository if one does not exist. Additionally, the [`datica git-remote`](#git-remote) command is used to manage git remotes and must be run inside of a git repository in order to work.

If you have more than one environment, you must specify which environment to use with the global `-E` flag.

Let's say you have initialized a code project for two environments (ex. "staging" and "production") named `mysandbox` and `myprod`. You have two options to specify which environment to run a command against.

First, you can tell the CLI which environment you want to use with the global option `-E` or `--env` (see [Global Options](#global-options)). Your command might start like this

```
datica -E myprod ...
```

If you don't set the `-E` flag, then the CLI picks one of your environments and prompts you to continue with this environment. This concept of scope will make it easier for Datica customers with multiple environments to use the CLI!

# Bash Autocompletion

One feature we've found helpful on \*Nix systems is autocompletion in bash. To enable this feature, head over to the github repo and download the `datica_autocomplete` file. If you use a Mac, you will need to install bash-completion with `brew install bash-completion` or `source` the `datica_autocomplete` file each time you start up a terminal. Store this file locally in `/etc/bash_completion.d/` or (`/usr/local/etc/bash_completion.d/` on a Mac). Completion will be available when you restart your terminal. Now type `datica ` and hit tab twice to see the list of available commands. **Please note** that autocompletion only works one level deep. The CLI will not autocomplete or suggest completions when you type `datica db ` and then hit tab twice. It currently only works when you have just `datica ` typed into your terminal. This is a feature we are looking into expanding in the future.

Note: you may have to add `source /etc/bash_completion.d/datica_autocomplete` (`/usr/local/etc/bash_completion.d/datica_autocomplete`) in your `~/.bashrc` (`~/.bash_profile`) file.

# Global Options

The following table outlines all global options available in the CLI. Global options are always set after the word `datica` and before any commands. Rather than setting these each time, you may also set an environment variable with the appropriate value which will automatically be used.

| Short Name | Long Name | Description | Environment Variable |
|------------|-----------|-------------|----------------------|
| &nbsp; | --email | Your Datica email that you login to the Dashboard with | DATICA_EMAIL |
| -U | --username | [DEPRECATED] Your Datica username that you login to the Dashboard with. Please use --email instead | DATICA_USERNAME |
| -P | --password | Your Datica password that you login to the Dashboard with | DATICA_PASSWORD |
| -E | --env | The name of the environment for which this command will be run. | DATICA_ENV |

# Overview

```

Usage: datica [OPTIONS] COMMAND [arg...]


Options:
  --email           Datica Email ($DATICA_EMAIL)
  -U, --username    [DEPRECATED] Datica Username (This flag is deprecated. Please use --email instead) ($DATICA_USERNAME)
  -P, --password    Datica Password ($DATICA_PASSWORD)
  -E, --env         The name of the environment in which this command will be run ($DATICA_ENV)
  -v, --version     Show the version and exit

Commands:
  certs          Manage your SSL certificates and domains
  clear          Clear out information in the global settings file to fix a misconfigured CLI.
  console        Open a secure console to a service
  db             Tasks for databases
  deploy         Deploy a Docker image to a container service.
  deploy-keys    Tasks for SSH deploy keys
  images         Operations for working with images
  domain         Print out the temporary domain name of the environment
  environments   Manage environments for which you have access
  files          Tasks for managing service files
  git-remote     Manage git remotes to Datica code services
  init           Get started using the Datica platform
  invites        Manage invitations for your organizations
  jobs           Perform operations on a service's jobs
  keys           Tasks for SSH keys
  logout         Clear the stored user information from your local machine
  logs           Show the logs in your terminal streamed from your logging dashboard
  maintenance    Manage maintenance mode for code services
  metrics        Print service and environment metrics in your local time zone
  rake           Execute a rake task
  redeploy       Redeploy a service without having to do a git push. This will cause downtime for all redeploys (see the resources page for more details).
  releases       Manage releases for code services
  rollback       Rollback a code service to a specific release
  services       Perform operations on an environment's services
  sites          Tasks for updating sites, including hostnames, SSL certificates, and private keys
  ssl            Perform operations on local certificates to verify their validity
  status         Get quick readout of the current status of an environment and all of its services
  support-ids    Print out various IDs related to an environment to be used when contacting Datica support
  update         Checks for available updates and updates the CLI if a new update is available
  users          Manage users who have access to the given organization
  vars           Interaction with environment variables for an environment
  version        Output the version and quit
  whoami         Retrieve your user ID
  worker         Manage a service's workers

Run 'datica COMMAND --help' for more information on a command.

```

# Certs

The `certs` command gives access to certificate and private key management for public facing services. The certs command cannot be run directly but has subcommands.

## Certs Create

```

Usage: datica certs create NAME ((PUBLIC_KEY_PATH PRIVATE_KEY_PATH [-s] [-r]) | -l)

Create a new domain with an SSL certificate and private key or create a Let's Encrypt certificate

Arguments:
  NAME=""               The name of this SSL certificate plus private key pair
  PUBLIC_KEY_PATH=""    The path to a public key file in PEM format
  PRIVATE_KEY_PATH=""   The path to an unencrypted private key file in PEM format

Options:
  -s, --self-signed=false    Whether or not the given SSL certificate and private key are self signed
  -r, --resolve=true         Whether or not to attempt to automatically resolve incomplete SSL certificate issues
  -l, --lets-encrypt=false   Whether or not this is a Let's Encrypt certificate

```

`certs create` allows you to upload an SSL certificate and private key which can be used to secure your public facing code service. Alternatively, you may opt to create a Let's Encrypt certificate. When creating a Let's Encrypt certificate, you only need to provide the certificate name along with the "-l" flag. Let's Encrypt certificates are issued asynchronously and may not be available immediately. Use the [certs list](#certs-list) command to check on the issuance status. Once issued, Let's Encrypt certificates automatically renew before expiring. Cert creation can be done at any time, even after environment provisioning, but must be done before [creating a site](#sites-create). When uploading a custom cert, the CLI will check to ensure the certificate and private key match. If you are using a self signed cert, pass in the `-s` flag and the hostname check will be skipped. Datica requires that your certificate file include your own certificate, intermediate certificates, and the root certificate in that order. If you only include your certificate, the CLI will attempt to resolve this and fetch intermediate and root certificates for you. It is advised that you create a full chain before running this command as the `-r` flag is accomplished on a "best effort" basis.

Here are a few sample commands

```
datica -E "<your_env_name>" certs create wildcard_mysitecom ~/path/to/cert.pem ~/path/to/priv.key
datica -E "<your_env_name>" certs create my.site.com --lets-encrypt
```

## Certs List

```

Usage: datica certs list

List all existing domains that have SSL certificate and private key pairs

```

`certs list` lists all of the available certs you have created on your environment. The displayed names are the names that should be used as the `CERT_NAME` parameter in the [sites create](#sites-create) command. If any certs are Let's Encrypt certs, the issuance status will also be shown. Here is a sample command

```
datica -E "<your_env_name>" certs list
```

## Certs Rm

```

Usage: datica certs rm NAME

Remove an existing domain and its associated SSL certificate and private key pair

Arguments:
  NAME=""      The name of the certificate to remove

```

`certs rm` allows you to delete old certificate and private key pairs. Only certs that are not in use by a site can be deleted. Here is a sample command

```
datica -E "<your_env_name>" certs rm mywebsite.com
```

## Certs Update

```

Usage: datica certs update NAME PUBLIC_KEY_PATH PRIVATE_KEY_PATH [-s] [-r]

Update the SSL certificate and private key pair for an existing domain

Arguments:
  NAME=""               The name of this SSL certificate and private key pair
  PUBLIC_KEY_PATH=""    The path to a public key file in PEM format
  PRIVATE_KEY_PATH=""   The path to an unencrypted private key file in PEM format

Options:
  -s, --self-signed=false   Whether or not the given SSL certificate and private key are self signed
  -r, --resolve=true        Whether or not to attempt to automatically resolve incomplete SSL certificate issues

```

`certs update` works nearly identical to the [certs create](#certs-create) command. All rules regarding self signed certs and certificate resolution from the `certs create` command apply to the `certs update` command. Let's Encrypt certs cannot be updated since they are automatically renewed before expiring. This is useful for when your certificates have expired and you need to upload new ones. Update your certs and then redeploy your service_proxy. Here is a sample command

```
datica -E "<your_env_name>" certs update mywebsite.com ~/path/to/new/cert.pem ~/path/to/new/priv.key
```

# Clear

```

Usage: datica clear [--private-key] [--session] [--environments] [--pods] [--all]

Clear out information in the global settings file to fix a misconfigured CLI.

Options:
  --private-key=false    Clear out the saved private key information
  --session=false        Clear out all session information
  --environments=false   Clear out all your environment data
  --pods=false           Clear out all saved pods
  --all=false            Clear out all settings

```

`clear` allows you to manage your global settings file in case your CLI becomes misconfigured. The global settings file is stored in your home directory at `~/.datica`. You can clear out all settings or pick and choose which ones need to be removed. After running the `clear` command, any other CLI command will reset the removed settings to their appropriate values. Here are some sample commands

```
datica clear --all
datica clear --environments # removes your locally cached environment(s) configuration data
datica clear --session --private-key # removes all session and private key authentication information
```

# Console

```

Usage: datica console SERVICE_NAME [COMMAND]

Open a secure console to a service

Arguments:
  SERVICE_NAME=""   The name of the service to open up a console for
  COMMAND=""        An optional command to run when the console becomes available

```

`console` gives you direct access to your database service or application shell. For example, if you open up a console to a postgres database, you will be given access to a psql prompt. You can also open up a mysql prompt, mongo cli prompt, rails console, django shell, and much more. When accessing a database service, the `COMMAND` argument is not needed because the appropriate prompt will be given to you. If you are connecting to an application service the `COMMAND` argument is required. Here are some sample commands

```
datica -E "<your_env_name>" console db01
datica -E "<your_env_name>" console app01 "bundle exec rails console"
```

# Db

The `db` command gives access to backup, import, and export services for databases. The db command can not be run directly but has subcommands.

## Db Backup

```

Usage: datica db backup DATABASE_NAME [-s]

Create a new backup

Arguments:
  DATABASE_NAME=""   The name of the database service to create a backup for (e.g. 'db01')

Options:
  -s, --skip-poll=false   Whether or not to wait for the backup to finish

```

`db backup` creates a new backup for the given database service. The backup is started and unless `-s` is specified, the CLI will poll every few seconds until it finishes. Regardless of a successful backup or not, the logs for the backup will be printed to the console when the backup is finished. If an error occurs and the logs are not printed, you can use the [db logs](#db-logs) command to print out historical backup job logs. Here is a sample command

```
datica -E "<your_env_name>" db backup db01
```

## Db Download

```

Usage: datica db download DATABASE_NAME BACKUP_ID FILEPATH [-f]

Download a previously created backup

Arguments:
  DATABASE_NAME=""   The name of the database service which was backed up (e.g. 'db01')
  BACKUP_ID=""       The ID of the backup to download (found from "datica backup list")
  FILEPATH=""        The location to save the downloaded backup to. This location must NOT already exist unless -f is specified

Options:
  -f, --force=false   If a file previously exists at "filepath", overwrite it and download the backup

```

`db download` downloads a previously created backup to your local hard drive. Be careful using this command as it could download PHI. Be sure that all hard drive encryption and necessary precautions have been taken before performing a download. The ID of the backup is found by first running the [db list](#db-list) command. Here is a sample command

```
datica -E "<your_env_name>" db download db01 cd2b4bce-2727-42d1-89e0-027bf3f1a203 ./db.sql
```

This assumes you are downloading a MySQL or PostgreSQL backup which takes the `.sql` file format. If you are downloading a mongo backup, the command might look like this

```
datica -E "<your_env_name>" db download db01 cd2b4bce-2727-42d1-89e0-027bf3f1a203 ./db.tar.gz
```

## Db Export

```

Usage: datica db export DATABASE_NAME FILEPATH [-f]

Export data from a database

Arguments:
  DATABASE_NAME=""   The name of the database to export data from (e.g. 'db01')
  FILEPATH=""        The location to save the exported data. This location must NOT already exist unless -f is specified

Options:
  -f, --force=false   If a file previously exists at `filepath`, overwrite it and export data

```

`db export` is a simple wrapper around the `db backup` and `db download` commands. When you request an export, a backup is created that will be added to the list of backups shown when you perform the [db list](#db-list) command. Then that backup is immediately downloaded. Regardless of a successful export or not, the logs for the backup will be printed to the console when the export is finished. If an error occurs and the logs are not printed, you can use the [db logs](#db-logs) command to print out historical backup job logs. Here is a sample command

```
datica -E "<your_env_name>" db export db01 ./dbexport.sql
```

This assumes you are exporting a MySQL or PostgreSQL database which takes the `.sql` file format. If you are exporting a mongo database, the command might look like this

```
datica -E "<your_env_name>" db export db01 ./dbexport.tar.gz
```

## Db Import

```

Usage: datica db import DATABASE_NAME FILEPATH [-s][-d [-c]]

Import data into a database

Arguments:
  DATABASE_NAME=""   The name of the database to import data to (e.g. 'db01')
  FILEPATH=""        The location of the file to import to the database

Options:
  -c, --mongo-collection=""   If importing into a mongo service, the name of the collection to import into
  -d, --mongo-database=""     If importing into a mongo service, the name of the database to import into
  -s, --skip-backup=false     Skip backing up database. Useful for large databases, which can have long backup times.

```

`db import` allows you to inject new data into your database service. For example, if you wrote a simple SQL file

```
CREATE TABLE mytable (
id TEXT PRIMARY KEY,
val TEXT
);

INSERT INTO mytable (id, val) values ('1', 'test');
```

and stored it at `./db.sql` you could import this into your database service. When importing data into mongo, you may specify the database and collection to import into using the `-d` and `-c` flags respectively. Regardless of a successful import or not, the logs for the import will be printed to the console when the import is finished. Before an import takes place, your database is backed up automatically in case any issues arise. Here is a sample command

```
datica -E "<your_env_name>" db import db01 ./db.sql
```

## Db List

```

Usage: datica db list DATABASE_NAME [-p] [-n]

List created backups

Arguments:
  DATABASE_NAME=""   The name of the database service to list backups for (e.g. 'db01')

Options:
  -p, --page=1         The page to view
  -n, --page-size=10   The number of items to show per page

```

`db list` lists all previously created backups. After listing backups you can copy the backup ID and use it to [download](#db-download) that backup or [view the logs](#db-logs) from that backup. Here is a sample command

```
datica -E "<your_env_name>" db list db01
```

## Db Logs

```

Usage: datica db logs DATABASE_NAME BACKUP_ID

Print out the logs from a previous database backup job

Arguments:
  DATABASE_NAME=""   The name of the database service (e.g. 'db01')
  BACKUP_ID=""       The ID of the backup to download logs from (found from "datica backup list")

```

`db logs` allows you to view backup logs from historical backup jobs. You can find the backup ID from using the `db list` command. Here is a sample command

```
datica -E "<your_env_name>" db logs db01 cd2b4bce-2727-42d1-89e0-027bf3f1a203
```

# Deploy

```

Usage: datica deploy SERVICE_NAME IMAGE_NAME

Deploy a Docker image to a container service.

Arguments:
  SERVICE_NAME=""   The name of the service to deploy to (e.g. 'container01')
  IMAGE_NAME=""     The name of the image to deploy (e.g. 'image01')

```

`deploy` deploys a Docker image for the given service. This command will only deploy for "container" services. Here is a sample command

```
datica -E "<your_env_name>" deploy container01 image01
```

# Deploy-keys

The `deploy-keys` command gives access to SSH deploy keys for environment services. The deploy-keys command can not be run directly but has subcommands.

## Deploy-keys Add

```

Usage: datica deploy-keys add NAME KEY_PATH SERVICE_NAME

Add a new deploy key

Arguments:
  NAME=""           The name for the new key, for your own purposes
  KEY_PATH=""       Relative path to the SSH key file
  SERVICE_NAME=""   The name of the code service to add this deploy key to

```

`deploy-keys add` allows you to upload an SSH public key in OpenSSH format. These keys are used for pushing code to your code services but are not required. You should be using personal SSH keys with the [keys](#keys) command unless you are pushing code from Continuous Integration or Continuous Deployment scenarios. Deploy keys are intended to be shared among an organization. Here are some sample commands

```
datica -E "<your_env_name>" deploy-keys add app01_public ~/.ssh/app01_rsa.pub app01
```

## Deploy-keys List

```

Usage: datica deploy-keys list SERVICE_NAME

List all deploy keys

Arguments:
  SERVICE_NAME=""   The name of the code service to list deploy keys

```

`deploy-keys list` will list all of your previously uploaded deploy keys by name including the key's fingerprint in SHA256 format. Here is a sample command

```
datica -E "<your_env_name>" deploy-keys list app01
```

## Deploy-keys Rm

```

Usage: datica deploy-keys rm NAME SERVICE_NAME

Remove a deploy key

Arguments:
  NAME=""           The name of the key to remove
  SERVICE_NAME=""   The name of the code service to remove this deploy key from

```

`deploy-keys rm` will remove a previously created deploy key by name. It is a good idea to rotate deploy keys on a set schedule as they are intended to be shared among an organization. Here are some sample commands

```
datica -E "<your_env_name>" deploy-keys rm app01_public app01
```

# Images

`images` allows interactions with container images and tags. This command cannot be run directly, but has subcommands.

## Images List

```

Usage: datica images list

List images available for an environment

```

`images list` lists available images for an environment. These images must be pushed to the registry for the environment in order to show. Example:
```
datica -E "<your_env_name>" images list```

## Images Tags

`tags` allows interactions with container image tags. This command cannot be run directly, but has subcommands.

### Images Tags List

```

Usage: datica images tags list IMAGE_NAME

List tags for a given image

Arguments:
  IMAGE_NAME=""   The name of the image to list tags for, including the environment's namespace.

```

List pushed tags for given image. Example:
```
datica -E "<your_env_name>" images tags list pod012345/my-image
```

### Images Tags Rm

```

Usage: datica images tags rm IMAGE_NAME TAG

Delete a tag for a given image

Arguments:
  IMAGE_NAME=""   The name of the image to list tags for, including the environment's namespace.
  TAG=""          The tag to delete.

```

Delete a tag for a given image. Example:
```
datica -E "<your_env_name>" images tags delete pod012345/my-image v1
```

# Domain

```

Usage: datica domain

Print out the temporary domain name of the environment

```

`domain` prints out the temporary domain name setup by Datica for an environment. This domain name typically takes the form podXXXXX.catalyzeapps.com but may vary based on the environment. Here is a sample command

```
datica -E "<your_env_name>" domain
```

# Environments

The `environments` command allows you to manage your environments. The environments command can not be run directly but has subcommands.

## Environments List

```

Usage: datica environments list

List all environments you have access to

```

`environments list` lists all environments that you are granted access to. These environments include those you created and those that other Datica customers have added you to. Here is a sample command

```
datica environments list
```

## Environments Rename

```

Usage: datica environments rename NAME

Rename an environment

Arguments:
  NAME=""      The new name of the environment

```

`environments rename` allows you to rename your environment. Here is a sample command

```
datica -E "<your_env_name>" environments rename MyNewEnvName
```

# Files

The `files` command gives access to service files on your environment's services. Service files can include Nginx configs, SSL certificates, and any other file that might be injected into your running service. The files command can not be run directly but has subcommands.

## Files Download

```

Usage: datica files download [SERVICE_NAME] FILE_NAME [-o] [-f]

Download a file to your localhost with the same file permissions as on the remote host or print it to stdout

Arguments:
  SERVICE_NAME="service_proxy"   The name of the service to download a file from
  FILE_NAME=""                   The name of the service file from running "datica files list"

Options:
  -o, --output=""     The downloaded file will be saved to the given location with the same file permissions as it has on the remote host. If those file permissions cannot be applied, a warning will be printed and default 0644 permissions applied. If no output is specified, stdout is used.
  -f, --force=false   If the specified output file already exists, automatically overwrite it

```

`files download` allows you to view the contents of a service file and save it to your local machine. Most service files are stored on your service_proxy and therefore you should not have to specify the `SERVICE_NAME` argument. Simply supply the `FILE_NAME` found from the [files list](#files-list) command and the contents of the file, as well as the permissions string, will be printed to your console. You can always store the file locally, applying the same permissions as those on the remote server, by specifying an output file with the `-o` flag. Here is a sample command

```
datica -E "<your_env_name>" files download /etc/nginx/sites-enabled/mywebsite.com
```

## Files List

```

Usage: datica files list [SERVICE_NAME]

List all files available for a given service

Arguments:
  SERVICE_NAME="service_proxy"   The name of the service to list files for

```

`files list` prints out a listing of all service files available for download. Nearly all service files are stored on the service_proxy and therefore you should not have to specify the `SERVICE_NAME` argument. Here is a sample command

```
datica -E "<your_env_name>" files list
```

# Git-remote

The `git-remote` command allows you to interact with code service remote git URLs. The git-remote command can not be run directly but has subcommands.

## Git-remote Add

```

Usage: datica git-remote add SERVICE_NAME [-r] [-f]

Add the git remote for the given code service to the local git repo

Arguments:
  SERVICE_NAME=""   The name of the service to add a git remote for

Options:
  -r, --remote="datica"   The name of the git remote to be added
  -f, --force=false       If a git remote with the specified name already exists, overwrite it

```

`git-remote add` adds the proper git remote to a local git repository with the given remote name and service. Here is a sample command

```
datica -E "<your_env_name>" git-remote add code-1 -r datica-code-1
```

## Git-remote Show

```

Usage: datica git-remote show SERVICE_NAME

Print out the git remote for a given code service

Arguments:
  SERVICE_NAME=""   The name of the service to add a git remote for

```

`git-remote show` prints out the git remote URL for the given service. This can be used to do a manual push or use the git remote for another purpose such as a CI integration. Here is a sample command

```
datica -E "<your_env_name>" git-remote show code-1
```

# Init

```

Usage: datica init

Get started using the Datica platform

```

The `init` command walks you through setting up the CLI to use with the Datica platform. The `init` command requires you to have an environment already setup.

# Invites

The `invites` command gives access to organization invitations. Every environment is owned by an organization and users join organizations in order to access individual environments. You can invite new users by email and manage pending invites through the CLI. You cannot call the `invites` command directly, but must call one of its subcommands.

## Invites Accept

```

Usage: datica invites accept INVITE_CODE

Accept an organization invite

Arguments:
  INVITE_CODE=""   The invite code that was sent in the invite email

```

`invites accept` is an alternative form of accepting an invitation sent by email. The invitation email you receive will have instructions as well as the invite code to use with this command. Here is a sample command

```
datica -E "<your_env_name>" invites accept 5a206aa8-04f4-4bc1-a017-ede7e6c7dbe2
```

## Invites List

```

Usage: datica invites list

List all pending organization invitations

```

`invites list` lists all pending invites for an environment's organization. Any invites that have already been accepted will not appear in this list. To manage users who have already accepted invitations or are already granted access to your environment, use the [users](#users) group of commands. Here is a sample command

```
datica -E "<your_env_name>" invites list
```

## Invites Rm

```

Usage: datica invites rm INVITE_ID

Remove a pending organization invitation

Arguments:
  INVITE_ID=""   The ID of an invitation to remove

```

`invites rm` removes a pending invitation found by using the [invites list](#invites-list) command. Once an invite has already been accepted, it cannot be removed. Removing an invitation is helpful if an email was misspelled or an invitation was sent to an incorrect email address. If you want to revoke access to a user who already has been given access to your environment, use the [users rm](#users-rm) command. Here is a sample command

```
datica -E "<your_env_name>" invites rm 78b5d0ed-f71c-47f7-a4c8-6c8c58c29db1
```

## Invites Send

```

Usage: datica invites send EMAIL

Send an invite to a user by email for a given organization

Arguments:
  EMAIL=""     The email of a user to invite to an environment. This user does not need to have a Datica account prior to sending the invitation

```

`invites send` invites a new user to your environment's organization. The only piece of information required is the email address to send the invitation to. The invited user will join the organization with no permissions. You must grant them permission through the dashboard. The recipient does **not** need to have a Dashboard account in order to send them an invitation. However, they will need to have a Dashboard account to accept the invitation. Here is a sample command

```
datica -E "<your_env_name>" invites send coworker@datica.com
```

# Jobs

The `jobs` command allows you to manage jobs for your service(s).  The jobs command cannot be run directly but has subcommands.

## Jobs List

```

Usage: datica jobs list SERVICE_NAME

List all jobs for a service

Arguments:
  SERVICE_NAME=""   The name of the service to list jobs for

```

`jobs list` prints out a list of all jobs in your environment and their current status. Here is a sample command:

```
datica -E "<your_env_name>" jobs list <your_service_name>
```

## Jobs Start

```

Usage: datica jobs start SERVICE_NAME JOB_ID

Start a specific job within a service

Arguments:
  SERVICE_NAME=""   The name of the service to list jobs for
  JOB_ID=""         The job ID for the job in service to be started

```

`jobs start` will start a job that is configured but not currently running within a given service. This command is useful for granual control of your services and their workers, tasks, etc. ```
datica -E "<your_env_name>" jobs start <your_service_name> <your_job_id>
```

## Jobs Stop

```

Usage: datica jobs stop [SERVICE_NAME] [JOB_ID] [-f]

Stop a specific job within a service

Arguments:
  SERVICE_NAME=""   The name of the service to list jobs for
  JOB_ID=""         The job ID for the job in service to be stopped

Options:
  -f, --force=false   Allow this command to be executed without prompting to confirm

```

`jobs stop` will shut down a running job within a given service. This command is useful for granular control of your services and their workers, tasks, etc. ```
datica -E "<your_env_name>" jobs stop <your_service_name> <your_job_id>
```

# Keys

The `keys` command gives access to SSH key management for your user account. SSH keys can be used for authentication and pushing code to the Datica platform. Any SSH keys added to your user account should not be shared but be treated as private SSH keys. Any SSH key uploaded to your user account will be able to be used with all code services and environments that you have access to. The keys command can not be run directly but has subcommands.

## Keys Add

```

Usage: datica keys add NAME PUBLIC_KEY_PATH

Add a public key

Arguments:
  NAME=""              The name for the new key, for your own purposes
  PUBLIC_KEY_PATH=""   Relative path to the public key file

```

`keys add` allows you to add a new SSH key to your user account. SSH keys added to your user account should be private and not shared with others. SSH keys can be used for authentication (as opposed to the traditional email and password) as well as pushing code to an environment's code services. Please note, you must specify the path to the public key file and not the private key. All SSH keys should be in either OpenSSH RSA format or PEM format. Here is a sample command

```
datica keys add my_prod_key ~/.ssh/prod_rsa.pub
```

## Keys List

```

Usage: datica keys list

List your public keys

```

`keys list` lists all public keys by name that have been uploaded to your user account including the key's fingerprint in SHA256 format. Here is a sample command

```
datica keys list
```

## Keys Rm

```

Usage: datica keys rm NAME

Remove a public key

Arguments:
  NAME=""      The name of the key to remove.

```

`keys rm` allows you to remove an SSH key previously uploaded to your account. The name of the key can be found by using the [keys list](#keys-list) command. Here is a sample command

```
datica keys rm my_prod_key
```

## Keys Set

```

Usage: datica keys set PRIVATE_KEY_PATH

Set your auth key

Arguments:
  PRIVATE_KEY_PATH=""   Relative path to the private key file

```

`keys set` allows the CLI to use an SSH key for authentication instead of the traditional email and password combination. This can be useful for automation or where shared workstations are involved. Please note that you must pass in the path to the private key and not the public key. The given key must already be added to your account by using the [keys add](#keys-add) command. Here is a sample command

```
datica keys set ~/.ssh/my_key
```

# Logout

```

Usage: datica logout

Clear the stored user information from your local machine

```

When using the CLI, your email and password are **never** stored in any file on your filesystem. However, in order to not type in your email and password each and every command, a session token is stored in the CLI's configuration file and used until it expires. `logout` removes this session token from the configuration file. Here is a sample command

```
datica logout
```

# Logs

```

Usage: datica logs [QUERY] [(-f | -t)] [--hours] [--minutes] [--seconds] [--service [(--job-id | --target)]]

Show the logs in your terminal streamed from your logging dashboard

Arguments:
  QUERY="*"    The query to send to your logging dashboard's elastic search (regex is supported)

Options:
  -f, --follow=false   Tail/follow the logs (Equivalent to -t)
  -t, --tail=false     Tail/follow the logs (Equivalent to -f)
  --hours=0            The number of hours before now (in combination with minutes and seconds) to retrieve logs
  --minutes=0          The number of minutes before now (in combination with hours and seconds) to retrieve logs
  --seconds=0          The number of seconds before now (in combination with hours and minutes) to retrieve logs
  --service=""         Query logs for a specific service label
  --job-id=""          Query logs for a particular job by id
  --target=""          Query logs for a particular procfile target

```

`logs` prints out your application logs directly from your logging Dashboard. If you do not see your logs, try adjusting the number of hours, minutes, or seconds of logs that are retrieved with the `--hours`, `--minutes`, and `--seconds` options respectively. To specify a specific service, job, or target use the '--service', '--job-id', and '--target' commands. You must specify a service to use '--job-id' or '--target', and you cannot specify both a job-id and a target at the same time. You can also follow the logs with the `-f` option. When using `-f` all logs will be printed to the console within the given time frame as well as any new logs that are sent to the logging Dashboard for the duration of the command. When using the `-f` option, hit ctrl-c to stop. Here are some sample commands

```
datica -E "<your_env_name>" logs --hours=6 --minutes=30
datica -E "<your_env_name>" logs -f
datica -E "<your_env_name>" logs --service="<your_service_name>"
datica -E "<your_env_name>" logs --service="<your_service_name>" --job-id="<your_job_id>"
```

# Maintenance

Maintenance mode can be enabled or disabled for code services on demand. This redirects all traffic to a default maintenance page.

## Maintenance Disable

```

Usage: datica maintenance disable SERVICE_NAME

Disable maintenance mode for a code service

Arguments:
  SERVICE_NAME=""   The name of the service to disable maintenance mode for

```

`maintenance disable` turns off maintenance mode for a given code service. Here is a sample command

```
datica -E "<your_env_name>" maintenance disable code-1
```

## Maintenance Enable

```

Usage: datica maintenance enable SERVICE_NAME

Enable maintenance mode for a code service

Arguments:
  SERVICE_NAME=""   The name of the code service to enable maintenance mode for

```

`maintenance enable` turns on maintenance mode for a given code service. Maintenance mode redirects all traffic for the given code service to a default HTTP maintenance page. If you would like to customize this maintenance page, please contact Datica support. Here is a sample command

```
datica -E "<your_env_name>" maintenance enable code-1
```

## Maintenance Show

```

Usage: datica maintenance show [SERVICE_NAME]

Show the status of maintenance mode for a code service

Arguments:
  SERVICE_NAME=""   The name of the code service to show the status of maintenance mode

```

`maintenance show` displays whether or not maintenance mode is enabled for a code service or all code services. Here are some sample commands

```
datica -E "<your_env_name>" maintenance show
datica -E "<your_env_name>" maintenance show code-1
```

# Metrics

The `metrics` command gives access to environment metrics or individual service metrics through a variety of formats. This is useful for checking on the status and performance of your application or environment as a whole. The metrics command cannot be run directly but has subcommands.

## Metrics Cpu

```

Usage: datica metrics cpu [SERVICE_NAME] [(--json | --csv | --text)] [--stream] [-m]

Print service and environment CPU metrics in your local time zone

Arguments:
  SERVICE_NAME=""   The name of the service to print metrics for

Options:
  --json=false     Output the data as json
  --csv=false      Output the data as csv
  --text=true      Output the data in plain text
  --stream=false   Repeat calls once per minute until this process is interrupted.
  -m, --mins=1     How many minutes worth of metrics to retrieve.

```

`metrics cpu` prints out CPU metrics for your environment or individual services. You can print out metrics in csv, json, plain text, or spark lines format. If you want plain text format, omit the `--json` and `--csv` flags. You can only stream metrics using plain text or spark lines formats. To print out metrics for every service in your environment, omit the `SERVICE_NAME` argument. Otherwise you may choose a service, such as an app service, to retrieve metrics for. Here are some sample commands

```
datica -E "<your_env_name>" metrics cpu
datica -E "<your_env_name>" metrics cpu app01 --stream
datica -E "<your_env_name>" metrics cpu --json
datica -E "<your_env_name>" metrics cpu db01 --csv -m 60
```

## Metrics Memory

```

Usage: datica metrics memory [SERVICE_NAME] [(--json | --csv | --text)] [--stream] [-m]

Print service and environment memory metrics in your local time zone

Arguments:
  SERVICE_NAME=""   The name of the service to print metrics for

Options:
  --json=false     Output the data as json
  --csv=false      Output the data as csv
  --text=true      Output the data in plain text
  --stream=false   Repeat calls once per minute until this process is interrupted.
  -m, --mins=1     How many minutes worth of metrics to retrieve.

```

`metrics memory` prints out memory metrics for your environment or individual services. You can print out metrics in csv, json, plain text, or spark lines format. If you want plain text format, omit the `--json` and `--csv` flags. You can only stream metrics using plain text or spark lines formats. To print out metrics for every service in your environment, omit the `SERVICE_NAME` argument. Otherwise you may choose a service, such as an app service, to retrieve metrics for. Here are some sample commands

```
datica -E "<your_env_name>" metrics memory
datica -E "<your_env_name>" metrics memory app01 --stream
datica -E "<your_env_name>" metrics memory --json
datica -E "<your_env_name>" metrics memory db01 --csv -m 60
```

## Metrics Network-in

```

Usage: datica metrics network-in [SERVICE_NAME] [(--json | --csv | --text)] [--stream] [-m]

Print service and environment received network data metrics in your local time zone

Arguments:
  SERVICE_NAME=""   The name of the service to print metrics for

Options:
  --json=false     Output the data as json
  --csv=false      Output the data as csv
  --text=true      Output the data in plain text
  --stream=false   Repeat calls once per minute until this process is interrupted.
  -m, --mins=1     How many minutes worth of metrics to retrieve.

```

`metrics network-in` prints out received network metrics for your environment or individual services. You can print out metrics in csv, json, plain text, or spark lines format. If you want plain text format, omit the `--json` and `--csv` flags. You can only stream metrics using plain text or spark lines formats. To print out metrics for every service in your environment, omit the `SERVICE_NAME` argument. Otherwise you may choose a service, such as an app service, to retrieve metrics for. Here are some sample commands

```
datica -E "<your_env_name>" metrics network-in
datica -E "<your_env_name>" metrics network-in app01 --stream
datica -E "<your_env_name>" metrics network-in --json
datica -E "<your_env_name>" metrics network-in db01 --csv -m 60
```

## Metrics Network-out

```

Usage: datica metrics network-out [SERVICE_NAME] [(--json | --csv | --text)] [--stream] [-m]

Print service and environment transmitted network data metrics in your local time zone

Arguments:
  SERVICE_NAME=""   The name of the service to print metrics for

Options:
  --json=false     Output the data as json
  --csv=false      Output the data as csv
  --text=true      Output the data in plain text
  --stream=false   Repeat calls once per minute until this process is interrupted.
  -m, --mins=1     How many minutes worth of metrics to retrieve.

```

`metrics network-out` prints out transmitted network metrics for your environment or individual services. You can print out metrics in csv, json, plain text, or spark lines format. If you want plain text format, simply omit the `--json` and `--csv` flags. You can only stream metrics using plain text or spark lines formats. To print out metrics for every service in your environment, omit the `SERVICE_NAME` argument. Otherwise you may choose a service, such as an app service, to retrieve metrics for. Here are some sample commands

```
datica -E "<your_env_name>" metrics network-out
datica -E "<your_env_name>" metrics network-out app01 --stream
datica -E "<your_env_name>" metrics network-out --json
datica -E "<your_env_name>" metrics network-out db01 --csv -m 60
```

# Rake

```

Usage: datica rake SERVICE_NAME TASK_NAME

Execute a rake task

Arguments:
  SERVICE_NAME=""   The service that will run the rake task.
  TASK_NAME=""      The name of the rake task to run

```

`rake` executes a rake task by its name asynchronously. Once executed, the output of the task can be seen through your logging Dashboard. Here is a sample command

```
datica -E "<your_env_name>" rake code-1 db:migrate
```

# Redeploy

```

Usage: datica redeploy SERVICE_NAME

Redeploy a service without having to do a git push. This will cause downtime for all redeploys (see the resources page for more details).

Arguments:
  SERVICE_NAME=""   The name of the service to redeploy (e.g. 'app01')

```

`redeploy` deploys an identical copy of the given service. For code services, this avoids having to perform a code push. You skip the git push and the build. For service proxies, new instances replace the old ones. All other service types cannot be redeployed with this command. For service proxy redeploys, there will be approximately 5 minutes of downtime. For code service redeploys, there will be approximately 30 seconds of downtime. Here is a sample command

```
datica -E "<your_env_name>" redeploy app01
```

# Releases

The `releases` command allows you to manage your code service releases. A release is automatically created each time you perform a git push. The release is tagged with the git SHA of the commit. Releases are a way of tagging specific points in time of your git history. By default, the last three releases will be kept. Please contact Support if you require more than the last three releases to be retained. You can rollback to a specific release by using the [rollback](#rollback) command. The releases command cannot be run directly but has subcommands.

## Releases List

```

Usage: datica releases list SERVICE_NAME

List all releases for a given code service

Arguments:
  SERVICE_NAME=""   The name of the service to list releases for

```

`releases list` lists all of the releases for a given service. A release is automatically created each time a git push is performed. Here is a sample command

```
datica -E "<your_env_name>" releases list code-1
```

## Releases Rm

```

Usage: datica releases rm SERVICE_NAME RELEASE_NAME

Remove a release from a code service

Arguments:
  SERVICE_NAME=""   The name of the service to remove a release from
  RELEASE_NAME=""   The name of the release to remove

```

`releases rm` removes an existing release. This is useful in the case of a misbehaving code service. Removing the release avoids the risk of rolling back to a "bad" build. Here is a sample command

```
datica -E "<your_env_name>" releases rm code-1 f93ced037f828dcaabccfc825e6d8d32cc5a1883
```

## Releases Update

```

Usage: datica releases update SERVICE_NAME RELEASE_NAME [--notes]

Update a release from a code service

Arguments:
  SERVICE_NAME=""   The name of the service to update a release for
  RELEASE_NAME=""   The name of the release to update

Options:
  -n, --notes=""   The new notes to save on the release.

```

`releases update` allows you to rename or add notes to an existing release. By default, releases are named with the git SHA of the commit used to create the release. Renaming them allows you to organize your releases. Here is a sample command

```
datica -E "<your_env_name>" releases update code-1 f93ced037f828dcaabccfc825e6d8d32cc5a1883 --notes "This is a stable build"
```

# Rollback

```

Usage: datica rollback SERVICE_NAME RELEASE_NAME

Rollback a code service to a specific release

Arguments:
  SERVICE_NAME=""   The name of the service to rollback
  RELEASE_NAME=""   The name of the release to rollback to

```

`rollback` is a way to redeploy older versions of your code service. You must specify the name of the service to rollback and the name of an existing release to rollback to. Releases can be found with the [releases list](#releases-list) command. Here are some sample commands

```
datica -E "<your_env_name>" rollback code-1 f93ced037f828dcaabccfc825e6d8d32cc5a1883
```

# Services

The `services` command allows you to manage your services. The services command cannot be run directly but has subcommands.

## Services List

```

Usage: datica services list

List all services for your environment

```

`services list` prints out a list of all services in your environment and their sizes. The services will be printed regardless of their currently running state. To see which services are currently running and which are not, use the [status](#status) command. Here is a sample command

```
datica -E "<your_env_name>" services list
```

## Services Stop

```

Usage: datica services stop SERVICE_NAME

Stop all instances of a given service (including all workers, rake tasks, and open consoles)

Arguments:
  SERVICE_NAME=""   The name of the service to stop

```

`services stop` shuts down all running instances of a given service. This is useful when performing maintenance on code services or services without volumes that must be shutdown to perform maintenance. Take caution when running this command as all instances of the service, all workers, all rake tasks, and all open console sessions will be stopped. Here is a sample command

```
datica -E "<your_env_name>" services stop code-1
```

## Services Rename

```

Usage: datica services rename SERVICE_NAME NEW_NAME

Rename a service

Arguments:
  SERVICE_NAME=""   The service to rename
  NEW_NAME=""       The new name for the service

```

`services rename` allows you to rename any service in your environment. Here is a sample command

```
datica -E "<your_env_name>" services rename code-1 api-svc
```

# Sites

The `sites` command gives access to hostname and SSL certificate usage for public facing services. `sites` are different from `certs` in that `sites` use an instance of a `cert` and are associated with a single service. `certs` can be used by multiple sites. The sites command can not be run directly but has subcommands.

## Sites Create

```

Usage: datica sites create SITE_NAME SERVICE_NAME (CERT_NAME | -l) [--client-max-body-size] [--proxy-connect-timeout] [--proxy-read-timeout] [--proxy-send-timeout] [--proxy-upstream-timeout] [--enable-cors] [--enable-websockets]

Create a new site linking it to an existing cert instance

Arguments:
  SITE_NAME=""      The name of the site to be created. This will be used in this site's nginx configuration file (e.g. ".example.com")
  SERVICE_NAME=""   The name of the service to add this site configuration to (e.g. 'app01')
  CERT_NAME=""      The name of the cert created with the 'certs' command (e.g. "star_example_com")

Options:
  --client-max-body-size=-1     The 'client_max_body_size' nginx config specified in megabytes
  --proxy-connect-timeout=-1    The 'proxy_connect_timeout' nginx config specified in seconds
  --proxy-read-timeout=-1       The 'proxy_read_timeout' nginx config specified in seconds
  --proxy-send-timeout=-1       The 'proxy_send_timeout' nginx config specified in seconds
  --proxy-upstream-timeout=-1   The 'proxy_next_upstream_timeout' nginx config specified in seconds
  --enable-cors=false           Enable or disable all features related to full CORS support
  --enable-websockets=false     Enable or disable all features related to full websockets support
  -l, --lets-encrypt=false      Whether or not this site should create an auto-renewing Let's Encrypt certificate

```

`sites create` allows you to create a site configuration that is tied to a single service. To create a site, you must specify an existing cert made by the [certs create](#certs-create) command or use the "-l" flag to automatically create a Let's Encrypt certificate. A site has three pieces of information: a name, the service it's tied to, and the cert instance it will use. The name is the `server_name` that will be injected into this site's Nginx configuration file. It is important that this site name match what URL your site will respond to. If this is a bare domain, using `mysite.com` is sufficient. If it should respond to the APEX domain and all subdomains, it should be named `.mysite.com` notice the leading `.`. The service is a code service that will use this site configuration. Lastly, the cert instance must be specified by the `CERT_NAME` argument used in the [certs create](#certs-create) command or by the "-l" flag indicating a new Let's Encrypt certificate should be created. You can also set Nginx configuration values directly by specifying one of the above flags. Specifying `--enable-cors` will add the following lines to your Nginx configuration

```
add_header 'Access-Control-Allow-Origin' '$http_origin' always;
add_header 'Access-Control-Allow-Credentials' 'true' always;
add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS, DELETE, PUT, HEAD, PATCH' always;
add_header 'Access-Control-Allow-Headers' 'DNT,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Accept,Authorization' always;
add_header 'Access-Control-Max-Age' 1728000 always;
if ($request_method = 'OPTIONS') {
  return 204;
}
```

Specifying `--enable-websockets` will add the following lines to your Nginx configuration

```
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

Here are some sample commands

```
datica -E "<your_env_name>" sites create .mysite.com app01 wildcard_mysitecom
datica -E "<your_env_name>" sites create .mysite.com app01 wildcard_mysitecom --client-max-body-size 50 --enable-cors
datica -E "<your_env_name>" sites create app01.mysite.com app01 --lets-encrypt --enable-websockets
```

## Sites List

```

Usage: datica sites list

List details for all site configurations

```

`sites list` lists all sites for the given environment. The names printed out can be used in the other sites commands. Here is a sample command

```
datica -E "<your_env_name>" sites list
```

## Sites Rm

```

Usage: datica sites rm NAME

Remove a site configuration

Arguments:
  NAME=""      The name of the site configuration to delete

```

`sites rm` allows you to remove a site by name. Since sites cannot be updated, if you want to change the name of a site, you must `rm` the site and then [create](#sites-create) it again. If you simply need to update your SSL certificates, you can use the [certs update](#certs-update) command on the cert instance used by the site in question. Here is a sample command

```
datica -E "<your_env_name>" sites rm mywebsite.com
```

## Sites Show

```

Usage: datica sites show NAME

Shows the details for a given site

Arguments:
  NAME=""      The name of the site configuration to show

```

`sites show` will print out detailed information for a single site. The name of the site can be found from the [sites list](#sites-list) command. Here is a sample command

```
datica -E "<your_env_name>" sites show mywebsite.com
```

# Ssl

The `ssl` command offers access to subcommands that deal with SSL certificates. You cannot run the SSL command directly but must call a subcommand.

## Ssl Resolve

```

Usage: datica ssl resolve CHAIN PRIVATE_KEY HOSTNAME [OUTPUT] [-f]

Verify that an SSL certificate is signed by a valid CA and attempt to resolve any incomplete certificate chains that are found

Arguments:
  CHAIN=""         The path to your full certificate chain in PEM format
  PRIVATE_KEY=""   The path to your private key in PEM format
  HOSTNAME=""      The hostname that should match your certificate (e.g. "*.datica.com")
  OUTPUT=""        The path of a file to save your properly resolved certificate chain (defaults to STDOUT)

Options:
  -f, --force=false   If an output file is specified and already exists, setting force to true will overwrite the existing output file

```

`ssl resolve` is a tool that will attempt to fix invalid SSL certificates chains. A well formatted SSL certificate will include your certificate, intermediate certificates, and root certificates. It should follow this format

```
-----BEGIN CERTIFICATE-----
<Your SSL certificate here>
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
<One or more intermediate certificates here>
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
<Root CA here>
-----END CERTIFICATE-----
```

If your certificate only includes your own certificate, such as the following format shows

```
-----BEGIN CERTIFICATE-----
<Your SSL certificate here>
-----END CERTIFICATE-----
```

then the SSL resolve command will attempt to resolve this by downloading public intermediate certificates and root certificates. A general rule of thumb is, if your certificate passes the `ssl resolve` check, it will almost always work on the Datica platform. You can specify where to save the updated chain or omit the `OUTPUT` argument to print it to STDOUT.

Please note you all certificates and private keys should be in PEM format. You cannot use self signed certificates with this command as they cannot be resolved as they are not signed by a valid CA. Here are some sample commands

```
datica ssl resolve ~/mysites_cert.pem ~/mysites_key.key *.mysite.com ~/updated_mysites_cert.pem -f
datica ssl resolve ~/mysites_cert.pem ~/mysites_key.key *.mysite.com
```

## Ssl Verify

```

Usage: datica ssl verify CHAIN PRIVATE_KEY HOSTNAME [-s]

Verify whether a certificate chain is complete and if it matches the given private key

Arguments:
  CHAIN=""         The path to your full certificate chain in PEM format
  PRIVATE_KEY=""   The path to your private key in PEM format
  HOSTNAME=""      The hostname that should match your certificate (e.g. "*.datica.com")

Options:
  -s, --self-signed=false   Whether or not the certificate is self signed. If set, chain verification is skipped

```

`ssl verify` will tell you if your SSL certificate and private key are properly formatted for use with Datica's Compliant Cloud. Before uploading a certificate to Datica you should verify it creates a full chain and matches the given private key with this command. Both your chain and private key should be **unencrypted** and in **PEM** format. The private key is the only key in the key file. However, for the chain, you should include your SSL certificate, intermediate certificates, and root certificate in the following order and format.

```
-----BEGIN CERTIFICATE-----
<Your SSL certificate here>
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
<One or more intermediate certificates here>
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
<Root CA here>
-----END CERTIFICATE-----
```

This command also requires you to specify the hostname that you are using the SSL certificate for in order to verify that the hostname matches what is in the chain. If it is a wildcard certificate, your hostname would be in the following format: `*.datica.com`. This command will verify a complete chain can be made from your certificate down through the intermediate certificates all the way to a root certificate that you have given or one found in your system.

You can also use this command to verify self-signed certificates match a given private key. To do so, add the `-s` option which will skip verifying the certificate to root chain and just tell you if your certificate matches your private key. Please note that the empty quotes are required for checking self signed certificates. This is the required parameter HOSTNAME which is ignored when checking self signed certificates. Here are some sample commands

```
datica ssl verify ./datica.crt ./datica.key *.datica.com
datica ssl verify ~/self-signed.crt ~/self-signed.key "" -s
```

# Status

```

Usage: datica status [--historical]

Get quick readout of the current status of an environment and all of its services

Options:
  --historical=false   If this option is specified, a complete history of jobs will be reported

```

`status` will give a quick readout of your environment's health. This includes your environment name, environment ID, and for each service the name, size, build status, deploy status, and service ID. Here is a sample command

```
datica -E "<your_env_name>" status
datica -E "<your_env_name>" status --historical
```

# Support-ids

```

Usage: datica support-ids

Print out various IDs related to an environment to be used when contacting Datica support

```

`support-ids` is helpful when contacting Datica support by submitting a ticket at https://datica.com/support. If you are having an issue with a CLI command or anything with your environment, it is helpful to run this command and copy the output into the initial correspondence with a Datica engineer. This will help Datica identify the environment faster and help come to resolution faster. Here is a sample command

```
datica -E "<your_env_name>" support-ids
```

# Update

```

Usage: datica update

Checks for available updates and updates the CLI if a new update is available

```

`update` is a shortcut to update your CLI instantly. If a newer version of the CLI is available, it will be downloaded and installed automatically. This is used when you want to apply an update before the CLI automatically applies it on its own. Here is a sample command

```
datica update
```

# Users

The `users` command allows you to manage who has access to your environment through the organization that owns the environment. The users command can not be run directly but has three subcommands.

## Users List

```

Usage: datica users list

List all users who have access to the given organization

```

`users list` shows every user that belongs to your environment's organization. Users who belong to your environment's organization may access to your environment's services and data depending on their role in the organization. Here is a sample command

```
datica -E "<your_env_name>" users list
```

## Users Rm

```

Usage: datica users rm EMAIL

Revoke access to the given organization for the given user

Arguments:
  EMAIL=""     The email address of the user to revoke access from for the given organization

```

`users rm` revokes a users access to your environment's organization. Revoking a user's access to your environment's organization will revoke their access to your organization's environments. Here is a sample command

```
datica -E "<your_env_name>" users rm user@example.com
```

# Vars

The `vars` command allows you to manage environment variables for your code services. The vars command can not be run directly but has subcommands.

## Vars List

```

Usage: datica vars list SERVICE_NAME [--json | --yaml]

List all environment variables

Arguments:
  SERVICE_NAME=""   The name of the service containing the environment variables.

Options:
  --json=false   Output environment variables in JSON format
  --yaml=false   Output environment variables in YAML format

```

`vars list` prints out all known environment variables for the given code service. You can print out environment variables in JSON or YAML format through the `--json` or `--yaml` flags. Here are some sample commands

```
datica -E "<your_env_name>" vars list code-1
datica -E "<your_env_name>" vars list code-1 --json
```

## Vars Set

```

Usage: datica vars set SERVICE_NAME (-v... | -f)

Set one or more new environment variables or update the values of existing ones

Arguments:
  SERVICE_NAME=""   The name of the service on which the environment variables will be set.

Options:
  -v, --variable    The env variable to set or update in the form "<key>=<value>"
  -f, --file=""     The path to a file to import environment variables from. This file can be in JSON, YAML, or KEY=VALUE format

```

`vars set` allows you to add new environment variables or update the value of an existing environment variable on the given code service. You can set/update 1 or more environment variables at a time with this command by repeating the `-v` option multiple times. Once new environment variables are added or values updated, a [redeploy](#redeploy) is required for the given code service to have access to the new values. The environment variables must be of the form `<key>=<value>`. Here is a sample command

```
datica -E "<your_env_name>" vars set code-1 -v AWS_ACCESS_KEY_ID=1234 -v AWS_SECRET_ACCESS_KEY=5678
```

## Vars Unset

```

Usage: datica vars unset SERVICE_NAME VARIABLE...

Unset (delete) an existing environment variable

Arguments:
  SERVICE_NAME=""   The name of the service on which the environment variables will be unset.
  VARIABLE          The names of environment variables to unset

```

`vars unset` removes environment variables from the given code service. Only the environment variable name is required to unset. Once environment variables are unset, a [redeploy](#redeploy) is required for the given code service to realize the variable was removed. You can unset any number of environment variables in one command. Here is a sample command

```
datica -E "<your_env_name>" vars unset code-1 AWS_ACCESS_KEY_ID AWS_SECRET_ACCES_KEY_ID
```

# Version

```

Usage: datica version

Output the version and quit

```

`version` prints out the current CLI version as well as the architecture it was built for (64-bit or 32-bit). This is useful to see if you have the latest version of the CLI and when working with Datica support engineers to ensure you have the correct CLI installed. Here is a sample command

```
datica version
```

# Whoami

```

Usage: datica whoami

Retrieve your user ID

```

`whoami` prints out the currently logged in user's users ID. This is used with Datica support engineers. Here is a sample command

```
datica whoami
```

# Worker

The `worker` command allows to deploy, list, remove, and scale the workers in a code service.

## Worker Deploy

```

Usage: datica worker deploy SERVICE_NAME TARGET

Deploy new workers for a given service

Arguments:
  SERVICE_NAME=""   The name of the service to use to deploy a worker
  TARGET=""         The name of the Procfile target to invoke as a worker

```

`worker deploy` allows you to start a background process asynchronously. The TARGET must be specified in your Procfile. Once the worker is started, any output can be found in your logging Dashboard or using the [logs](#logs) command. Here is a sample command

```
datica -E "<your_env_name>" worker deploy code-1 mailer
```

## Worker List

```

Usage: datica worker list SERVICE_NAME

Lists all workers for a given service

Arguments:
  SERVICE_NAME=""   The name of the service to list workers for

```

`worker list` lists all workers and their scale for a given code service along with the number of currently running instances of each worker target. Here is a sample command

```
datica -E "<your_env_name>" worker list code-1
```

## Worker Rm

```

Usage: datica worker rm SERVICE_NAME TARGET

Remove all workers for a given service and target

Arguments:
  SERVICE_NAME=""   The name of the service running the workers
  TARGET=""         The worker target to remove

```

`worker rm` removes a worker by the given TARGET and stops all currently running instances of that TARGET. Here is a sample command

```
datica -E "<your_env_name>" worker rm code-1 mailer
```

## Worker Scale

```

Usage: datica worker scale SERVICE_NAME TARGET SCALE

Scale existing workers up or down for a given service and target

Arguments:
  SERVICE_NAME=""   The name of the service running the workers
  TARGET=""         The worker target to scale up or down
  SCALE=""          The new scale (or change in scale) for the given worker target. This can be a single value (e.g. 2) representing the final number of workers that should be running. Or this can be a change represented by a plus or minus sign followed by the value (e.g. +2 or -1). When using a change in value, be sure to insert the "--" operator to signal the end of options. For example, "datica worker scale code-1 worker -- -1"

```

`worker scale` allows you to scale up or down a given worker TARGET. Scaling up will launch new instances of the worker TARGET while scaling down will immediately stop running instances of the worker TARGET if applicable. Here are some sample commands

```
datica -E "<your_env_name>" worker scale code-1 mailer 1
datica -E "<your_env_name>" worker scale code-1 mailer -- -2
```
