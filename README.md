# proxy-network

This project uses docker to expose an HTTPS reverse proxy server with Nginx and automatically generated SSL certificates from LetsEncrypt.

This project is based on the following wonderful containers:

- https://github.com/jwilder/nginx-proxy
- https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion

## Getting Started

Clone this repository on your web hosting instance

### Start the proxy-network

1. `$ cd proxy-network`
2. `$ docker-compose up -d`

### Connecting services

Now you can connect other container services to the proxy-network by setting the following environment variables on the service container:

      - VIRTUAL_HOST: subdomain.somewebsite.com # should be a valid, publicly reachable domain(s)
      - LETSENCRYPT_HOST: subdomain.somewebsite.com # same as above
      - LETSENCRYPT_EMAIL: someguy@someemail.com # must be a valid email, Let's Encrypt will use this to send warnings about expiring SSL certificates.

and connect to the proxy network: `proxy-network_default`

#### docker run example

```
$ docker run -d --name proxied-nginx \
--network=proxy-network_default \
-e "VIRTUAL_HOST=subdomain.somewebsite.com" \
-e "LETSENCRYPT_HOST=subdomain.somewebsite.com" \
-e "LETSENCRYPT_EMAIL=someguy@someemail.com" \
nginx
```

#### docker-compose example

```
version: '3.1'

services:

  wordpress:
    image: wordpress:latest
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb
      VIRTUAL_HOST: wp.somewebsite.com
      LETSENCRYPT_HOST: wp.somewebsite.com
      LETSENCRYPT_EMAIL: someguy@someemail.com

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: exampledb
      MYSQL_USER: exampleuser
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1'

networks:
        default:
                external:
                        name: proxy-network_default

```
