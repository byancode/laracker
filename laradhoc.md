Laracker
===

![GitHub stars](https://img.shields.io/github/stars/eleftrik/laracker?style=social)

![GitHub watchers](https://img.shields.io/github/watchers/eleftrik/laracker?style=social)

![GitHub issues](https://img.shields.io/github/issues/eleftrik/laracker)

![GitHub](https://img.shields.io/github/license/eleftrik/laracker)

![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/eleftrik/laracker?label=version)

🆕 ✅ *Version **2.2.0** is out with some improvement!*

**Laracker** is a Docker-based basic LEMP development environment designed for [Laravel](https://laravel.com/)
applications.

Looking for a similar Docker environment for [WordPress](https://wordpress.org/)? Then give a try to
[Dockpress](https://github.com/eleftrik/dockpress)!

[Preferisci leggere in italiano? 🇮🇹](README.it.md)

## Features

* Nginx
* PHP (7.2 / 7.3 / 7.4 / 8.0) with OPCache
* Composer 2.0
* MySQL / MariaDB
* MongoDB
* phpMyAdmin
* Mailhog
* Redis
* Custom domain name (es. `http://laracker.test` or `https://laracker.test`)
* HTTP or HTTPS (with self-signed SSL certificate)
* npm
* gulp (for old projects)

You can choose which version of PHP (for example, `7.4` ) to run by setting `$PHP_VERSION` variable in your
`.env` file (see `.env.example` for details).

Likewise, you can choose your database (for example, `MariaDB 10.2` ) by setting `$DATABASE_IMAGE` variable in your
`.env` file (see `.env.example` for details)

In case you want to customize your Docker configuration (e.g. adding some mount), just
run `cp docker-compose.yml docker-compose.override.yml` then edit your
`docker-compose.override.yml` . It will be used by Docker.

## Requirements

* MacOS, Linux or Windows with WSL
* Docker
* `openssl` (when using HTTPS)

## Installation

Just clone this repo.

Let's pretend your Laravel application will be accessible at `laracker.test` :

```bash
git clone git@github.com:eleftrik/laracker.git laracker.test
cd laracker.test
```

## `laracker` command

Before version `2.1.0` , you had to

* `cd` into Laracker folder
* invoke `./bin/laracker`

Starting from version `2.1.0` , you can invoke `laracker` no matter which folder you're in.

**Tip** If you want to invoke `laracker` command when you're into your Laravel folder, define an
alias `alias laracker='../bin/laracker'` (e.g. in your `.bashrc` file).

In this way, when you're using your terminal in the root folder of you Laravel project, you can simply type `laracker` (
as if the binary were in the same folder).

## Configuration

### .env

Create an `.env` file from `.env.example`

```bash
cp .laracker .env

# Customize every variable according to your needs
# See comments to each variable in .env.example file
```

### Custom domain

According to the value of `${APP_HOST}` , add your test domain (e.g. `laracker.test` ) to your hosts file

```bash
sudo /bin/bash -c 'echo -e "127.0.0.1 laracker.test" >> /etc/hosts'
```

### HTTPS

Choose if you want to run your application over HTTP or HTTPS.

* HTTPS: set `NGINX_ENABLE_HTTPS=1`
* HTTP: set `NGINX_ENABLE_HTTPS=0`

With HTTPS enabled, a self-signed SSL certificate will be generate.

Under `.docker/images/nginx/ssl` you will find

* `${APP_HOST}.test.crt`
* `${APP_HOST}.test.key`

NOTE: you need to import/install the `.crt` file so it will be trusted by your operating system / browser.

### Let's go

Build all Docker containers and start them

```bash
 ./bin/laracker init
```

---

Ok, let's talk now about your Laravel application!

New or existing?

### a. New application

New Laravel project from scratch? No problem, just run:

```bash
./bin/laracker install-laravel
```

A fresh Laravel app will be downloaded in `${APP_SRC}` , configured and available at [http://${APP_HOST}]()
or [https://${APP_HOST}]()

### b. Existing application

Do you have an existing Laravel Project?

Just copy it or clone it into `${APP_SRC}` so your Laravel application is inside that directory.

Then run:

```bash
./bin/laracker init-laravel
```

Laracker will search for an `.env` file (if not found, it will try to copy `.env.example` ). Then, it will update your
Laravel `.env` with the values taken from the main `.env` file. After this, it will install Composer dependencies and a
key will be generated by the usual Artisan command.

---

Remember to run manually your migrations / seeds:

```bash
./bin/laracker artisan migrate --seed
```

Do whatever your Laravel application needs to run correctly. For example:

* `./bin/laracker artisan passport:install --force`
* `./bin/laracker node npm install && ./bin/laracker node npm run dev`
* etc.

--- 

Finished working? Just stop everything:

```bash
./bin/laracker stop
```

Next time you need to run your application, if you haven't changed any setting, just run

```bash
./bin/laracker 
```

<b>Please note</b> Nginx will proxy all request from `socket.io` to `laravel-echo` container.

## Updates

When updating from a previous version, follow these steps:

* update your code
    - via `git pull` if you're still referencing this repository, a fork or a private one
    - manually downloading the desired [release](https://github.com/eleftrik/laracker/releases)

  In both cases, the `src/` folder won't be affected
* see `CHANGELOG.md`
* update your `./.env` file according to `./.env.example`
  (new variables may have been introduced)
* if you have overridden `docker-compose.yml` using `docker-compose.override.yml`, see
`docker-compose.yml` to check if something has added, changed or deleted, compared to the previous version
  of `docker-compose.yml` you were using before updating
* launch `./bin/laracker start --build`

## Scripts

Laracker provides some useful scripts, located in `.docker/scripts` .

Run them from your Laracker base folder.

### init

```bash
./bin/laracker init
```

It will

* check `openssl` is installed on your host machine (if you set `NGINX_ENABLE_HTTPS=1`)
* create a self-signed certificate (if you set `NGINX_ENABLE_HTTPS=1`)
* build and start the containers

### start | up

```bash
./bin/laracker start
```

or

```bash
./bin/laracker up
```

It's a shortcut to

```bash
docker-compose up -d
```

### build

If you want to (re)build the images, use

```bash
./bin/laracker build
```

### stop | down

Tired of working? Stop the environment

```bash
./bin/laracker stop
```

or

```bash
./bin/laracker down
```

### install-laravel

It's useful to bring up a new Laravel project. It will prepare a fresh Laravel app in your `${APP_SRC}` , create
a `${APP_SRC}/.env` file holding the same values which are in the main `.env` file

```bash
./bin/laracker install-laravel
```

### init-laravel

Update the Laravel `.env` with the environment values coming from the main `.env` file

```bash
./bin/laracker init-laravel
```

### artisan

It will execute an artisan command inside the php-fpm container. For example:

```bash
./bin/laracker artisan make:migration create_example_table
```

### composer

It will execute a *composer* command through the `composer` container. For example:

```bash
./bin/laracker composer install
```

### node

It will execute commands through the `node` container. For example:

```bash
./bin/laracker node yarn run production
```

### gulp

Need to run some "old" project still using `gulp` ? No problem: this command will run Gulp and compile your assets.

For example:

```bash
./bin/laracker gulp watch
```

### test

This command will execute PHPUnit tests:

```bash
./bin/laracker test
```

### ps

This command will show running containers, according to docker-compose file:

```bash
./bin/laracker ps
```

It's a shortcut to

```bash
docker-compose ps
```

### nah

Want to throw away **anything**? This command will stop all containers, delete volumes and the entire `$APP_SRC` .

So, before executing this command, **BE SURE** you understood very well that you're going to lose all your Laravel
codebase and the related database!

```bash
./bin/laracker nah
```

To throw away anything and start again from the scratch, use

```bash
./bin/laracker nah && ./bin/laracker init && ./bin/laracker laravel-install
```

### help

This command will show a quick help, listing all available commands:

```bash
./bin/laracker help
```

## Accessing MySQL/MariaDB database

### Via phpMyAdmin

You can use phpMyAdmin: [http://${APP_HOST}:${PHPMYADMIN_PORT}]()

For example: [http://laracker.test:8080]()

### Via client

You can connect to your database via command line or using a tool.

For example, from the command line:

```bash
source .env
mysql -u$MYSQL_USER -p$MYSQL_PASSWORD -h127.0.0.1 $MYSQL_DATABASE
```

I bet you prefer to use your favorite tool, for example:

* TablePlus
* SequelPro
* HeidiSQL

etc.

Just use the parameters stored in your .env file.

## Accessing MongoDB

MongoDB is listening on port 27017.

In the following examples, of course you have to replace
`user` , `password` and `laracker` with the current values of your
`MONGODB_USER` , `MONGODB_PASSWORD` and `MONGODB_DATABASE` environment variables.

If in your host you did install *mongo* CLI, you can access through command line:

```shell script
mongo -u user -p password laracker

```

Otherwise, you can access through with your favourite tool, using this connection string:

`mongodb://user:password@localhost/laracker`

## MailHog

To catch all outgoing emails via MailHog, configure your Laravel `.env` file with these parameters:

```dotenv
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=
MAIL_PASSWORD=
```

MailHog web interface is available at

[http://${APP_HOST}:${MAILHOG_PORT}]()

For example: [http://laracker.test:8081]()

## Contributing

Suggestions, reviews, bug reports are very welcome. We never stop learning :-)

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## Thanks

Thanks to

* [Mauro Cerone](https://github.com/ceronem)
* Sail by [Taylor Otwell](https://github.com/taylorotwell)

for the inspiration

## License

[MIT](https://choosealicense.com/licenses/mit/)
