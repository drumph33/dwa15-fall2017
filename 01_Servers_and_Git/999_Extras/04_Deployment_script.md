# Deployment script
Note: Setting up a deployment script for your projects in this course is __optional__. If you don't wish to dig into shell scripting, you can continue to deploy using the manual procedures covered in Weeks 1 and 2.

## Preface
When we're ready to deploy changes to our production servers, we start by locally adding and committing all files we wish to deploy, for example:

```bash
$ git add --all
$ git commit -m 'Added form validation'
```

Then, we run the following 5 commands to deploy these changes on our production servers.

1. Push changes to our master repository in Github:
```bash
$ git push origin master
```

2. SSH into the production server:
```bash
ssh root@ip.address
```

3. Move into the project directory (Laravel project `foobooks` used here as an example):
```bash
cd /var/www/html/foobooks
```

4. Pull the latest changes:
```bash
git pull origin master
```

5. Update packages (if a Laravel-based project):
```bash
composer install
```

Running these 5 commands can be streamlined into a single command via a __deployment script__.

A deployment script is a [shell script](https://www.panix.com/~elflord/unix/bash-tute.html) (i.e. a command line script) that lets you group together a series of commands.

We can streamline the process even further by creating a [git alias](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases) to git push and invoke your deployment script.


## Setup
### Step 1) Local setup
Locally, [copy the contents of this file](https://raw.githubusercontent.com/susanBuck/dwa15-fall2017/master/01_Servers_and_Git/999_Extras/deploy.sh) and save it as `bash/deploy.sh` in your project (you'll need to create the subdirectory `bash`).

Locally, give the deploy script executable [permissions](https://github.com/susanBuck/dwa15-fall2017/blob/master/00_Command_Line/99_Extras/Permissions.md) (`+x`):
```
$ chmod +x bash/deploy.sh
```

Add, commit, and push this new file:
```
$ git add bash/deploy.sh
$ git commit -m 'Added deploy script'
$ git push origin master
```


### Step 2) Server setup
SSH into your server to pull the latest changes (including this new script).

```xml
$ ssh root@digital.ocean.ip.address
$ cd /var/www/html/foobooks
$ git pull origin master
```

With the deploy script now on your server, give it executable [permissions](https://github.com/susanBuck/dwa15-fall2017/blob/master/00_Command_Line/99_Extras/Permissions.md) (`+x`)

```xml
$ chmod +x /var/www/html/foobooks/bash/deploy.sh
```


### Step 3) Setup alias locally
Locally, in the root of your project run the following command to create a new git alias for `pd` (short for `push deploy`):

```bash
$ git config alias.pd '!git push origin master && bash/deploy.sh'
```

This creates an alias for the command `!git push origin master && bash/deploy.sh` which will push any commits to your repo and then invoke the deploy script to deploy the changes on your server.

To test it out, run the following:

```
$ git pd
```

You should see a prompt like the following, assuming you have no changed files on your production server. Note that you're prompted to continue with deployment:

```xml
Everything up-to-date
Detected location: Local
--------------------------------------
Git status on server for /var/www/html/foobooks:
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
--------------------------------------
Do you want to continue with deployment? (y/n)
```

If you do have changed files on your server you'll see them listed, and you may want to abort deployment (by typing `n`) so that you can resolve any potential conflicts before they occur.

If you continue with deployment (by typing `y`), you'll see something like this, which shows the output of a `git pull origin master` followed by a `composer install`:

```xml
Do you want to continue with deployment? (y/n)
y
Detected location: Server - Running deployment.
--------------------------------------
git pull origin master:
From github.com:susanBuck/foobooks
 * branch            master     -> FETCH_HEAD
Already up-to-date.
--------------------------------------
composer install:
Do not run Composer as root/super user! See https://getcomposer.org/root for details
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Nothing to install or update
Generating optimized autoload files
> Illuminate\Foundation\ComposerScripts::postAutoloadDump
> @php artisan package:discover
Discovered Package: fideloper/proxy
Discovered Package: laravel/tinker
Package manifest generated successfully.
```


## Common problems and tips

### Git alias
You can edit your project's git alias' by editing the file `.git/config`

Example alias set in `.git/config`:

```xml
[alias]
        pd = !git push origin master && bash/deploy.sh
```

### Problem: deploy.sh: Permission denied
If you receive the following message when trying to run your deploy script...
```bash
-bash: ./bash/deploy.sh: Permission denied#
```

...make sure you gave the script executable permissions (on both your local *and* server copy):

```bash
chmod +x bash/deploy.sh
```

When running `ls -l` your permissions for the deployment script should be set to `-rw-r--r--`. Example:

```bash
$ cd bash
$ ls -l
total 8
-rw-r--r--   1 Susan  admin  1314 Oct  9 16:36 deploy.sh
```


## More about shell scripting
Shell scripts use their own syntax language. Looking at the provided `deploy.sh` you can see the syntax for basic programming paradigms such as variables, functions, and switch statements. [You can learn more about the basics of writing shell scripts here...](https://www.panix.com/~elflord/unix/bash-tute.html)