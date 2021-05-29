# Local development using Docker

## Adding a new site

In the .env file add the necessary env vars. Modify the `docker-compose.yaml` file to accommodate new sites. Be sure 
to up the port values (to prevent conflict between containers).

Add the database in the `config/01-databases.sql` file.

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

This setup currently has a simple WordPress PHP container (apache), MySql 5.7, WP-CLI and Xdebug for debugging purposes.
A mailhog container is also added, but it's not fully configured yet. Also, there are some issues with using step 
debugging with Xdebug, so this needs to be resolved.
