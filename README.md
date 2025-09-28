# Overview

This Docker image provides a fully runnable instance of Contao, the open-source CMS for professional websites and scalable web applications. Designed for ease of use and rapid deployment, the image includes all necessary dependencies, making it ideal for development, testing, or production environments.


# Usage

To start a Contao container:

    docker run -d -p 80:80 potagos/contao

Visit http://localhost/contao-manager.phar.php/ in your browser to begin the Contao installation.

# Docker Compose
For installation and usage you need a SQL-Database. The image supports mysql-based Databases. Following is an example docker-compose.yaml to host contao locally.

```
services:
  database:
    image: mariadb
    environment:
      MARIADB_ROOT_PASSWORD: supersecretpassword_pleasechange
      MARIADB_DATABASE: contao
      MARIADB_USER: contao
      MARIADB_PASSWORD: secretpassord_pleasechange
    networks:
      - app

  contao:
    image: potagos/contao:5.5
    ports:
      - 80:80
    networks:
      - app

networks:
  app:
```

In the installation you need to configure the Database connection as `mysql://contao:secretpassord_pleasechange@database:3306/contao`

## Volumes
To persist changes after restart I remmonend adding the following volume mounts to compose:
```
services:
  database:
    image: mariadb
    volumes:
      - ./db:/var/lib/mysql
    environment:
      MARIADB_ROOT_PASSWORD: supersecretpassword_pleasechange
      MARIADB_DATABASE: contao
      MARIADB_USER: contao
      MARIADB_PASSWORD: secretpassord_pleasechange
    networks:
      - app

  contao:
    image: potagos/contao:5.5
    volumes:
      - ./contao/system/config:/var/www/html/contao/system/config
      - ./contao/templates:/var/www/html/contao/templates
      - ./contao/files:/var/www/html/contao/files
      - ./contao/src:/var/www/html/contao/src
      - ./contao/var/logs:/var/www/html/contao/var/logs
    networks:
      - app

networks:
  app:
```

# Thanks
I want to give huge thanks to Raphael Stäbler (productionbuild), who made a [blog post](https://raphaelstaebler.info/en/blog/run-contao-4-inside-a-docker-container/) for creating a contao image for contao 4.4.