# Moodle LMS docker
to deploy moodle, we need moodle images and dbms (mariadb, msql) images

## pull image

- [moodle image](https://hub.docker.com/repository/docker/mridho/lms-moodle)

```
$ docker pull mridho/lmsmoodle:latest
```

- [dbms image (mariadb)](https://hub.docker.com/_/mariadb)

```
$ docker pull mariadb:10.5.3
```

## deploy interactively

- create user-define network

```
$ docker network create moodle
```

- run mariadb container

```
$ mkdir /var/lib/mysql-moodle

$ docker run --name=mariadb-moodle --network=moodle \
   -e MYSQL_ROOT_PASSWORD=m00dle \
   -e MYSQL_USER=moodle \
   -e MYSQL_PASSWORD=m00dle \
   -e MYSQL_DATABASE=lms-moodle \
   -v /var/lib/mysql-moodle:/var/lib/mysql \
   mariadb:latest
```

- run moodle container
```
$ docker run --name=moodle-container --network=moodle -d -p 80:80 mridho/lms-moodle:latest
```

## deploy using docker-compose

docker-compose file [link](https://github.com/ridhohafidz/docker-moodle/blob/master/docker-compose.yml)

```
version: '3'
services:
    db:
        image: mariadb:10.5.3
        volumes:
            - moodle_data:/var/lib/mysql
        restart: always
        environment:
            MYSQL_ROOT_PASSWORD: m00dle
            MYSQL_DATABASE: lms-moodle
            MYSQL_USER: moodle
            MYSQL_PASSWORD: m00dle
        networks:
            - moodle
        
    app:
        depends_on: 
            - db
        image: mridho/lms-moodle:latest
        ports:
            - "80:80"
        restart: always
	networks:
	    - moodle

volumes:
    moodle_data:        

networks:
    moodle:
```
