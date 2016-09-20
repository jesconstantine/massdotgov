# Massdotgov sandbox D8 repo

A sandbox repository to learn Drupal 8, particularly related pilot Mass.gov

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project to a cloud instance.

### Prerequisities

- *AMP stack or preferrably [Acquia Dev Desktop](https://www.acquia.com/downloads)
- Sign up for a free [acquia cloud account](https://accounts.acquia.com/user?site=insight#action=user-register)
- Membership to Acquia Cloud Team Subscription (see Jes)
- Github account
- Membership to Mass Digital Services FREND bitbucket team (see Jes)
- Create SSH key ([instructions](https://docs.acquia.com/dev-desktop/keygen))
- Add SSH key to your Acquia Cloud account ([instructions](https://docs.acquia.com/cloud/ssh/enable/add-key)) 
- Add SSH key to your github ([instructions](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)) account
- Download Drush aliases and Acquia Cloud api credentials and store them in your home directory ([instructions](https://docs.acquia.com/cloud/drush-aliases))

### Installation Steps
Follow these steps to get the three instances cloned into your local dev desktop

> **Before You Start:**
> 
> In order to connect with the acquia cloud instances in your dev desktop, you have to have a free acquia cloud account set up with an ssh key, and you must be part of our acquia cloud team. (See Prerequisites above)
> Once you've been added to the subscription/team, follow the next steps to clone the instances in your local dev desktop

#### Starting with an Acquia Cloud site in Dev Desktop

Follow [these steps](https://docs.acquia.com/dev-desktop/start/cloud) to import the acquia cloud site into your local dev desktop.

> **Notes:**
> - At Step 3, make sure you select the dev environment
> 
> - At Step 4, use the PHP 5.6* version
> - **At Step 4, if your site is appended with .dev - remove it, this should modify both your site and database names**
> - At Step 5, keep the clone database and file checkboxes checked 
> - At Step 6, keep the sanitize the database unchecked 

(If you're interested in what is happening behind the scenes here, see the ['How Dev Desktop works with Acquia Cloud' docs page](https://docs.acquia.com/dev-desktop/cloud/details#clone).)

#### Working with local and cloud sites in Acquia Dev Desktop

Familiarize yourself with what is available in the Dev Desktop control panel for working with your [local sites](https://docs.acquia.com/dev-desktop/sites) and our [cloud sites](https://docs.acquia.com/dev-desktop/cloud/working).


## Set Up for Local Drupal Development

You'll want to have some local settings files set up:

### settings.local.php

This file is not versioned.  We currently use it to turn on verbose logging, tell Drupal not to aggregate js/css, and to use a `development services.yml` file.  (We tell Drupal about this file in our settings.php file @~#717)

```Shell
cd docroot/sites && cp example.settings.local.php ./default/settings.local.php 
```

### development.services.yml

This file should already exist in docroot/sites/ directory and is versioned.  (should we not version this?)  We currently use it to turn on twig and cache tag debugging.  (We tell Drupal about this file in our settings.local.php @~#39)


#### Turn on Twig Debugging

```YAML
parameters:
  twig.config:
    debug: true
    auto_reload: true
    cache: false
```

#### Turn on Debug for Cacheable Responses

```YAML
parameters:
  twig.config:
    #... <snip>
  http.response.debug_cacheability_headers: true
```

### Drush aliases

Ensure you have `massdotgov.dev` (+ `.test`) as an alias.  To check, list all of your aliases:

```Shell
drush sa
```

You should see `massdotgov.dev` etc in your list of aliases.  If you don't, see Prerequisites above for instructions on downloaded drush aliases.

## Logging in to Sites

Feel free to create your own credentials, otherwise:

- Username: `admin`
- Password: `admin`

## Set up Git Remotes

- Rename acquia codebase, currently origin, to 'acquia' (optional):
```git remote rename origin acquia```

- Create single source of truth called 'upstream' (can we create this in massgov github?):
```git remote add upstream git@github.com:jesconstantine/massdotgov.git```

- Create your fork of `github` in your personal github account and add your fork as a remote: [instructions](https://help.github.com/articles/fork-a-repo/)


## Git Workflow

Fork + Pull Request

Do your work in a feature/ branch

Let's get together and talk about this - who merges? who accepts pull requests?  etc...

## Configuration Management

We keep all site configuration in code and use drush to sync that configuration up and down our stack `local -> dev -> staging -> production`.  Please note this workflow is only possible with clones of the site (same uuid) and not across other instances.

First we tell Drupal about our name and location for storing staged configuration (active configuration is stored in the DB) in settings.php @~#250 we set a value for `$config_directories['vcs']` to a directory that is sibling to docroot.

### Configuration Export (local)

After making any configuration changes, export the configuration from your local database:

```Shell
drush config-export vcs
```

(See additional [command options](https://drushcommands.com/drush-8x/config/config-export/))

After confirming your export, you should see the changes (.yml config files) reflected in git:

```Shell
git status
```

### Configuration Import (cloud)

After your code has been deployed to a cloud instance (see Deployment below), you need to get your staged configuration (the .yml files) imported into the database:

```Shell
drush @massdotgov.dev config-import vcs
```

(See additional [command options](https://drushcommands.com/drush-8x/config/config-import/))

## Deployment

Use git, drush and acquia cloud api commands to push your code (`feature/` branch for feature testing, `develmassdotgovop` branch after an accepted pull request, or `tag` on master for releases).

Push your branch upstream (the acquia cloud codebase):


## Pro Tip: Set Up your Text Editor / IDE

Since we are working with version control, git notices every difference in a tracked file - even when someone has their text editor setup to use tabs and another has their editor set up to use spaces.  If we are not all on the same page, git will think every file we open has been changed.  (yuck!)

To mitigate this, projects conform to agreed upon coding standards which can be enforced through code hinting/linting tools as well as an editor normalization process called [EditorConfig](http://editorconfig.org/).

### Install EditorConfig plugin for your editor / IDE
See [editorconfig.org/#download](http://editorconfig.org/#download)

### Drupal Coding Standards and EditorConfig

In the root of every Drupal 8 site, there is an .editorconfig file which tells editors and IDEs how to facilitate parts of<sup id="a1">[ [1] ](#f1)</sup> this editor normalization process according to [Drupal coding standards](https://www.drupal.org/coding-standards).

Here are the contents of that file (as of 6/20/2016):

```Ini
# Drupal editor configuration normalization
# @see http://editorconfig.org/

# This is the top-most .editorconfig file; do not search in parent directories.
root = true

# All files.
[*]
end_of_line = LF
indent_style = space
indent_size = 2
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[composer.json]
indent_size = 4
```

This tells your editor that, for all files:
- Assigns lf (Linefeed) as the line ending format (learn more: [Newline](https://en.wikipedia.org/wiki/Newline))
- When you hit <kbd>Tab</kbd> use spaces instead of a tab character
- When you hit <kbd>Tab</kbd> indent the line by 2 spaces
- Use a charset of utf-8
- Trim any whitespace that is left at the end of a line
- Make sure every file ends with a blank line

And only for the `composer.json` file:
- When you hit <kbd>Tab</kbd> indent the line by 4 spaces

---

> <b id="f1">[1]</b> The .editorconfig file will handle basic setup of your editor/IDE like indenting, whitespace, and line length but will not hint or lint at syntax for Operators, Control Structures, Functions, etc.  But there are [code sniffing tools](https://www.drupal.org/node/1419980) which can handle that in various ways throughout your development workflow. [(Go Back)](#a1)
