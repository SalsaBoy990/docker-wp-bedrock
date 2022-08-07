# WordPress | Bedrock - Local development environment with Docker

Images used for the services:

- Wordpress: **wordpress:php8.0-fpm**
- Nginx: **nginx:stable-alpine**
- MariaDB: **mariadb:10.2**
- PhpMyAdmin: **phpmyadmin/phpmyadmin**

`wp-cli` and `xdebug` support included.


## Setup

1. Create `.env` file in root folder. See the example. Change the app name and the app domain only!

2. Create `wordpress` folder in root. Bedrock will be installed here.

3. Create SSL certificate for APP_DOMAIN with `bin/setup-ssl`. Make sure `server_name` (there is 2 of them), `ssl_certificate`, and `ssl_certificate_key` is correct in `.docker/nginx/default.conf`! Although, the script will replace these values with the APP_DOMAIN if the default values are **wordpress.local** `.docker/nginx/default.conf`.

You may need a different executable for mkcert. The supplied mkcert version is for Debian/Ubuntu.
Links:

- Windows: https://github.com/FiloSottile/mkcert#windows
- Mac: https://github.com/FiloSottile/mkcert#macos
- Linux: https://github.com/FiloSottile/mkcert#linux or use pre-build binaries: https://github.com/FiloSottile/mkcert/releases

4. Add domain alias for 127.0.0.1 (e.g. `vim /etc/hosts`)

5. Build docker project: 

```shell
(set -a;source .env;docker-compose -f docker-compose-ssl.yml up --build)
```

For Linux, you may have to use `sudo` (if your Docker is not configured to be used as a non-root user).
There are useful bash scripts in `bin` folder (`bin/start`, `bin/down`, etc.) which are useful.

6. Create a new Bedrock project:

`bin/composer create-project roots/bedrock`

7. Unfortunately, Bedrock will create the project into a subfolder inside the `wordpress` folder, so you need to
copy the subfolder content into the `wordpress` folder. And delete the subfolder afterwards.

8. Make sure the `.env` credentials are correct (in wordpress folder, especially, the `WP_HOME` should point to your custom domain) and match with the variable defined in `.env` in the root folder
You can customize the DB_PREFIX to increase security.


## WP CLI

Run `bin/wp` to enter the `wordpress` container. Simply run `wp`.
The wp-cli is run by the www-data user, not by root (otherwise, it would be a huge security risk).


## DB

You should add your sql dumpfiles into the db folder to be imported. Check out the mysql shell scripts in `bin` folder.
Or use PhpMyAdmin.


## PHP > 8.0 not supported

On PHP 8.1, there are deprecation errors for preg_replace(), trim(), rtrim():

```
Deprecated: preg_replace(): Passing null to parameter #3 ($subject) of type array|string is deprecated in /var/www/html/web/wp/wp-includes/formatting.php on line 5385
Deprecated: trim(): Passing null to parameter #1 ($string) of type string is deprecated in /var/www/html/web/wp/wp-includes/pluggable.php on line 604
Deprecated: rtrim(): Passing null to parameter #1 ($string) of type string is deprecated in /var/www/html/web/wp/wp-includes/formatting.php on line 2772
```

PHP 8.0 is the highest version that works for Bedrock. :(


## Other

This works better for Docker:
`wordpress/config/environments/development.php` -> `Config::define('FS_METHOD', 'direct');`

*TODO: More work on these issues:*
You may need to change permissions for wordpress folder. There are still permission issues.

However, for production, the folders use the permission 755, and 644 for the files! 777 is a very-very bad and dangerous idea for production. Never do it!


## Change Language

In composer.json changer these packages:
```
"koodimonni-language/hu_hu": "*",
"koodimonni-plugin-language/woocommerce-hu_hu": "6.7.0",
```
to your language and versions.
See more at: https://wp-languages.github.io/ and https://discourse.roots.io/t/install-update-wordpress-languages-with-composer/2021

Copy the needed files to `wordpress/app/languages`. Also create themes and plugins subfolders, and copy the right files in the appropriate place.
`wordpress/vendor/koodimonni-language`
`wordpress/vendor/koodimonni-plugin-language`
`wordpress/vendor/koodimonni-theme-language`


## Useful resources

- Create dummy data for testing:
https://wpattire.com/tips/how-to-add-dummy-data-in-woocommerce/

- The WP theme used:
https://github.com/bootscore

- Auto create woocommerce pages
https://quadlayers.com/create-woocommerce-pages/

