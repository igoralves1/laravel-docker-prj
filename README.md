# docker-compose-laravel
A pretty simplified Docker Compose workflow that sets up a LEMP network of containers for local Laravel development. You can view the full article that inspired this repo [here](https://dev.to/aschmelyun/the-beauty-of-docker-for-local-laravel-development-13c0).


## Usage

To get started, make sure you have [Docker installed](https://docs.docker.com/docker-for-mac/install/) on your system, and then clone this repository.

Next, navigate in your terminal to the directory you cloned this, and spin up the containers for the web server by running `docker-compose up -d --build site`.

After that completes, follow the steps from the [src/README.md](src/README.md) file to get your Laravel project added in (or create a new blank one).

Bringing up the Docker Compose network with `site` instead of just using `up`, ensures that only our site's containers are brought up at the start, instead of all of the command containers as well. The following are built for our web server, with their exposed ports detailed:

- **nginx** - `:80`
- **mysql** - `:3306`
- **php** - `:9000`
- **redis** - `:6379`
- **mailhog** - `:8025` 

Three additional containers are included that handle Composer, NPM, and Artisan commands *without* having to have these platforms installed on your local computer. Use the following command examples from your project root, modifying them to fit your particular use case.

- `docker-compose run --rm composer update`
- `docker-compose run --rm npm run dev`
- `docker-compose run --rm artisan migrate` 

## Persistent MySQL Storage

By default, whenever you bring down the Docker network, your MySQL data will be removed after the containers are destroyed. If you would like to have persistent data that remains after bringing containers down and back up, do the following:

1. Create a `mysql` folder in the project root, alongside the `nginx` and `src` folders.
2. Under the mysql service in your `docker-compose.yml` file, add the following lines:

```
volumes:
  - ./mysql:/var/lib/mysql
```

## Using BrowserSync with Laravel Mix

If you want to enable the hot-reloading that comes with Laravel Mix's BrowserSync option, you'll have to follow a few small steps. First, ensure that you're using the updated `docker-compose.yml` with the `:3000` and `:3001` ports open on the npm service. Then, add the following to the end of your Laravel project's `webpack.mix.js` file:

```javascript
.browserSync({
    proxy: 'nginx',
    open: false,
    port: 3000,
});
```

From your terminal window at the project root, run the following command to start watching for changes with the npm container and its mapped ports:

```bash
docker-compose run --rm --service-ports npm run watch
```

That should keep a small info pane open in your terminal (which you can exit with Ctrl + C). Visiting [localhost:3000](http://localhost:3000) in your browser should then load up your Laravel application with BrowserSync enabled and hot-reloading active.

## MailHog

The current version of Laravel (8 as of today) uses MailHog as the default application for testing email sending and general SMTP work during local development. Using the provided Docker Hub image, getting an instance set up and ready is simple and straight-forward. The service is included in the `docker-compose.yml` file, and spins up alongside the webserver and database services.

To see the dashboard and view any emails coming through the system, visit [localhost:8025](http://localhost:8025) after running `docker-compose up -d site`.


# =====================

# docker-compose-laravel
A pretty simplified Docker Compose workflow that sets up a LEMP network of containers for local Laravel development. You can view the full article that inspired this repo [here](https://dev.to/aschmelyun/the-beauty-of-docker-for-local-laravel-development-13c0). [The beauty of Docker for local Laravel development](https://dev.to/aschmelyun/the-beauty-of-docker-for-local-laravel-development-13c0)


## Usage

To get started, make sure you have [Docker installed](https://docs.docker.com/docker-for-mac/install/) on your system, and then clone this repository.

Next, navigate in your terminal to the directory you cloned this, and spin up the containers for the web server by running `docker-compose up -d --build site`.

After that completes, follow the steps from the [src/README.md](src/README.md) file to get your Laravel project added in (or create a new blank one).

Bringing up the Docker Compose network with `site` instead of just using `up`, ensures that only our site's containers are brought up at the start, instead of all of the command containers as well. The following are built for our web server, with their exposed ports detailed:

- **nginx** - `:8080`
- **mysql** - `:3306`
- **php** - `:9000`

Three additional containers are included that handle Composer, NPM, and Artisan commands *without* having to have these platforms installed on your local computer. Use the following command examples from your project root, modifying them to fit your particular use case. From inside `docker-compose-laravel/src(master)$` run the following commands:

0 - Usage of `compose`
```
docker-compose run --rm <compose_command>
```

1 - Generate the key
```
docker-compose run --rm artisan key:generate
```
2 - Install dependences with composer
```
docker-compose run --rm composer install
```
3 - Update composer
```
docker-compose run --rm composer update
```
4 - Run npm
```
docker-compose run --rm npm run dev
```
5 - Run migrations
```
docker-compose run --rm artisan migrate
```
6 - To SSH into the MySql container 
```
docker exec -it <mysql container ID>  /bin/bash
```

## Persistent MySQL Storage

By default, whenever you bring down the Docker network, your MySQL data will be removed after the containers are destroyed. If you would like to have persistent data that remains after bringing containers down and back up, do the following:

1. Create a `mysql` folder in the project root, alongside the `nginx` and `src` folders.
2. Under the `mysql` service in your `docker-compose.yml` file, add the following lines:

```
volumes:
  - ./mysql:/var/lib/mysql
```

It is available some startup MySql configuration at `docker-compose.yml` in the `mysql` session. By default it is created a database called `homestead`, a user called `homestead` with the password `secret`. The MySql root password is also created with a password `secret`. To get SSH access to the MySql container   
```docker exec -it <mysql container ID>  /bin/bash```

>If having ```SQLSTATE[HY000] [2002] Connection refused (SQL: select * from information_schema.tables where table_schema = laravel and table_name = migrations and table_type = 'BASE TABLE')``` just replace `DB_HOST=127.0.0,1` or `DB_HOST=0.0.0.0` to `DB_HOST=mysql`.  
>This is the final configuration in `.env`:  
```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret
```
If the MySql Container is already running and want to create a permanent storage
1 - stop and remove mysql container:
```
docker stop mysql && docker rm mysql
```
2 - rebuild project:
```
docker-compose up -d --build site
```


Issues with Mysql connection refer to `https://github.com/aschmelyun/docker-compose-laravel/issues/48` or `https://github.com/aschmelyun/docker-compose-laravel/issues/70`  

Note: using MySql-Workbench this is the configuration:
```
Hostname: 127.0.0.1
Port: 3306
Username: homestead
Default Schema: homestead
Password: secret
```

Note:
MySql - Laravel Backup 
A modern backup solution for Laravel apps -> https://github.com/spatie/laravel-backup

## Some useful commands:
1 - Check Containers
```
docker ps -a
```
2 - Stop all Containers
```
docker stop $(docker ps -a -q)
```
3 - Remove all Containers
```
docker rm $(docker ps -a -q)
```
4 - Check Containers
```
docker ps -a
```
5 - Check Images
```
docker images
```
6 - Remove all Images
```
docker rmi -f $(docker images -a -q)
```
7 - Check Images
```
docker images
```
8 - Build the site
```
docker-compose up -d --build site
```
9 - Remove all containers at once
```
docker ps -a && docker stop $(docker ps -a -q) && docker rm $(docker ps -a -q) && docker ps -a && docker images && docker rmi -f $(docker images -a -q) && docker images
```


## How to pull or create a Laravel project

1 - Clone your project OR Create a new project OR  Copy all of the files directly into this `src/` directory.
2 - Build the Docker service:   
```
docker-compose up -d --build site
```
3 - In your terminal go to the `docker-compose-laravel/src` folder:   
```
cd docker-compose-laravel/src
```
5 - Remove the `README.md`:   
```
cd src/ && rm README.md
```
6 - Create a new Laravel project:    
```
docker-compose run --rm composer create-project laravel/laravel .
```
7 - Example:   
```
~/vhosts/docker-compose-laravel/src(master)$ docker-compose run --rm composer create-project laravel/laravel .
```

