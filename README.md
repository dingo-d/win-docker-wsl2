# Local development using Docker with WSL2

The following is a Docker based WordPress development setup that works for me on Windows 10 with WSL2.
You'll need to enable WSL2, download Docker Desktop and configure it to work with WSL2. Once you do that, you can log in to the WSL2 with your terminal (I use the Windows Terminal), and go to your root folder (`~`).

It's **imperative** that all your work is originating from the linux root, and not from the mounted drive, because that can be quite slow, and things like Webpack hot reload won't work when you run it from your terminal.

My setup (from within WSL) looks like this:

```bash
~/Sites
  |____Projects
  | |____first-project
  | |____second-project
  | |____...
  |____config
  | |____01-databases.sql
  | |____php.ini
  |____.env
  |____docker-compose.yaml
```

*Side note:* You can access your WSL folders in the explorer by typing `\\wsl$\Ubuntu-20.04\...` depending on which Linux distribution you chose as a basis for the WSL.

## Architecture

The idea is pretty simple: run each app in its own container, but share other services (like a database or mailhog).  

Why? Because we don't need to (over)complicate things. This setup is used for local development, not for production.

This way we get the best of both worlds: we have a separation of concerns, as far as our apps go, but we can use 3rd party software to connect to a single database instance and see all the databases in one place.

## Adding a new site

In the .env file add the necessary env vars. Modify the `docker-compose.yaml` file to accommodate new sites. Be sure to increase the port values (to prevent conflict between containers).

Add the database name in the `config/01-databases.sql` file (has to be the same as in the `.env` file).

Run the `docker compose up -d`.

The sites should be available on `localhost:PORT` url.

## Multisites

If you have issues fetching the URLS from multisite setups, be sure to add a `.htaccess` file that looks like this

```apacheconf
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /
  RewriteRule ^index\.php$ - [L]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule . /index.php [L]
</IfModule>

```

## What's included?

This setup currently has a simple WordPress PHP container (apache), MySql 5.7, WP-CLI and Xdebug for debugging 
purposes and Mailhog for 'catching' emails coming from the WordPress app.

## Using WP-CLI with docker

Because our apps live in containers, and not on our system, that means we cannot just type `wp plugin list` and hope that we'll get a list of the plugins added to our app. The apps live in a container. So we must actually SSH to the container and run the WP-CLI command there. 

You can do that by typing

```bash
wp plugin list --ssh=docker-compose:first-project
```

From the root where your `docker-compose.yaml` is located. That way, WP-CLI knows which container to use when running the command.

