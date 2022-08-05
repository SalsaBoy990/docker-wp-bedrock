# WordPress | Bedrock - Local development environment with Docker

Services: PHP-FPM, Nginx, MariaDB, PhpMyAdmin.

`wp-cli` and `xdebug` support.

## Setup

1. Create `.env` file in root folder. See the example. Change the app name only!

2. Create `wordpress` folder in root. Bedrock will be installed here.

3. Run `docker-compose up --build`. There are useful shell commands in `bin` folder.

4. Create a new Bedrock project

`bin/composer create-project roots/bedrock`

5. Make sure the `.env` credentials are correct (in wordpress folder) and match with the variable defined in `.env` in the root folder
You can customize the DB_PREFIX to increase security.


## WP CLI

Run `bin/wp` to enter the `wordpress` container. Simply run `wp`.
The wp-cli is run by the www-data user, not by root (it would be a huge security risk).


## DB

You should add your sql dumpfiles into the db folder to be imported. Check out the mysql shell scripts in `bin` folder.
Or use PhpMyAdmin.


