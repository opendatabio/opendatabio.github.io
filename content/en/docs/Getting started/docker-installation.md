---
title: "Docker Installation"
linkTitle: "Docker Installation"
weight: 2
description: >
  How to install OpenDataBio with Docker
---

> The easiest way to install and run OpenDataBio is using [Docker](https://www.docker.com/) and the docker configuration files provided, which contain all the needed configurations to run ODB. Uses nginx and mysql, and supervisor for queues

{{< alert color="warning" >}}Docker configuration files provided for test or development only{{< /alert >}}

### Docker files
```bash
laraverl-app/
----docker/*
----./env.docker
----docker-compose.yml
----Dockerfile
----Makefile
```
These are adapted from [this link](https://github.com/dimadeush/docker-nginx-php-laravel), where you find a production setting as well.

### Installation

{{< github_button button="view" user="opendatabio" repo="opendatabio" large="true" >}}

1. Make sure you have [Docker](https://www.docker.com/) and [Docker-compose](https://docs.docker.com/compose/install/) installed in your system;
1. Check if your user is in the [docker group](https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md) created during docker installation;
1. Download or clone the OpenDataBio in your machine;
1. Make sure your user is the owner of the opendatabio folder created and contents, else change ownership and group recursively to your user
1. Enter the opendatabio directory
1. Edit and adjust the environment file name `.env.docker` (optional)
1. To install locally for development just adjust the following variables in the Dockerfile, which are needed to map the files owners to a docker user;
    1. `UID` the numeric user your are logged in and which is the owner of all files and directories in the opendatabio directory.
    1. `GDI` the numeric group the user belongs, usually same as UID.
1. File `Makefile` contains shortcuts to the _docker-compose_ commands used to build the services configured in the `docker-compose.yml` and auxiliary files in the `docker` folder.
1. Build the docker containers using the shortcuts (read the Makefile to undersand the commands)
```bash
make build
```
1. Start the implemented docker Services
```bash
make start
```
1. See the containers and try log into the laravel container
```bash
docker ps
make ssh #to enter the container shell
make ssh-mysql #to enter the mysql container, where you may access the database shell using `mysql -uroot -p` or use the laravel user
```

1. Install composer dependencies
```bash
make composer-install
```

1. Migrate the database
```bash
make migrate
```

1. If worked, then Opendatabio will be available in your browser [http::/localhost:8080](http::/localhost:8080).
1. Login with superuser `admin@example.org` and password `password1`
1. Additional configurations in these files are required for a production environment and deployment;

### Data persistence

The docker images may be deleted without loosing any data.
The mysql tables are stored in a volume. You may change to a local path bind.
```bash
docker volume list
```

### Using
See the contents of Makefile
```bash
make stop
make start
make restart
docker ps
...
```

If you have issues and changed the docker files, you may need to rebuild:

```bash
#delete all images without loosing data
make stop
docker system prune -a  #and accepts Yes
make build
make start
```
