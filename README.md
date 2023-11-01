# e-learning

- ```traefik chmod 600 traefik/acme.json```

- https://hub.docker.com/r/bitnami/moodle

- creat a .env file
```js
   MOODLE_USERNAME=
   MOODLE_PASSWORD=
   MOODLE_EMAIL=
   MOODLE_DATABASE_USER=
   MOODLE_DATABASE_NAME=
   MOODLE_DATABASE_PASSWORD=
```



Backup
- docker cp leroy-moodle-1:/bitnami ./server
- docker cp leroy-mariadb-1:/bitnami ./serverdb
- docker exec leroy-mariadb-1 mariadb-dump --all-databases -uroot > serverdb.sq

Restore 
- docker cp ./server1 leroy-moodle-1:/bitnami
- docker cp ./server1db leroy-mariadb-1:/bitnami
- docker exec -i leroy-mariadb-1 sh -c 'exec mariadb -uroot' < serverdb.sq

-----------------------


Maintenance
Backing up your container
To backup your data, configuration and logs, follow these simple steps:

Step 1: Stop the currently running container
docker stop moodle
Or using Docker Compose:

docker-compose stop moodle
Step 2: Run the backup command
We need to mount two volumes in a container we will use to create the backup: a directory on your host to store the backup in, and the volumes from the container we just stopped so we can access the data.

docker run --rm -v /path/to/moodle-backups:/backups --volumes-from moodle busybox \
  cp -a /bitnami/moodle /backups/latest
Restoring a backup
Restoring a backup is as simple as mounting the backup as volumes in the containers.

For the MariaDB database container:

 $ docker run -d --name mariadb \
   ...
-  --volume /path/to/mariadb-persistence:/bitnami/mariadb \
+  --volume /path/to/mariadb-backups/latest:/bitnami/mariadb \
   bitnami/mariadb:latest
For the Moodle™ container:

 $ docker run -d --name moodle \
   ...
-  --volume /path/to/moodle-persistence:/bitnami/moodle \
+  --volume /path/to/moodle-backups/latest:/bitnami/moodle \
   bitnami/moodle:latest

--------------------


Logicial Backups
Backup
mariadb-dump is in the Docker Official Image and can be used as follows:

docker exec some-%%REPO%% mariadb-dump --all-databases -uroot -p"$MARIADB_ROOT_PASSWORD"' 
> /some/path/on/your/host/all-databases.sql
Restoring Data from Dump Files
For restoring data, you can use the `docker exec` command with the `-i` flag, similar to the following:

docker exec -i some-%%REPO%% sh -c 'exec mariadb -uroot -p"$MARIADB_ROOT_PASSWORD"' 
< /some/path/on/your/host/all-databases.sql

- https://docs.docker.com/storage/volumes/#back-up-restore-or-migrate-data-volumes