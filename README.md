# Deploy `PostgresSQL` and `pgAdmin` on Docker



[Docker](https://www.docker.com/) is an easy way to provide a suitable environment to build, test and deploy your projects. Today, most of the software tech companies and developers are using containered environments to setup their services.

Here, we used [docker compose](https://docs.docker.com/compose/) plugin to build and deploy `postgres` and `pgAdmin` services.



**Steps to write `Postgres` and `pgAdmin` configuration files:**

- Create a  directory for configuration files in your desired path (Ex `postgres_docker`)

- Create a [`docker-compose.yml`](https://docs.docker.com/compose/compose-file/) configuration file to define services

  ```yaml
  version: "3"
  services:
    postgres:
      image: postgres:14
      container_name: postgresql
      networks:
        - db-net
      env_file: ./postgres_env
      volumes:
        - "./postgres-data:/var/lib/postgresql/data"
    pgadmin:
      image: dpage/pgadmin4:6.8
      container_name: pg-admin
      networks:
        - db-net
      env_file: ./pgadmin_env
      ports:
        - "80:80"
      volumes:
        - "./pgadmin-data:/var/lib/pgadmin"
  
  networks:
    db-net:
    	driver: bridge
  ```

  

- Create `postgres_env` file for *postgres* container environments:

  ```shell
  POSTGRES_USER=root
  POSTGRES_PASSWORD=root
  ```

- Create `pgadmin_env` file for *pgAdmin* container environments:

  ```shell
  PGADMIN_DEFAULT_EMAIL=root@example.com
  PGADMIN_DEFAULT_PASSWORD=root
  ```



**Note: Persist `pgAdmin` container data**

To give `pgAdmin` enough privilege to write on its own volume, the mapped directory's user & group ownership should be 5050 (pgadmin user's ID)

```shell
# In your project's directory
# First, make pgadmin-data directory (if you do't create it ;) )
mkdir pgadmin-data

# Then, change ownership's UID & GID to 5050
sudo chown 5050:5050 pgadmin-data/
```


Now `pgAdmin` container has enough privilege to write data on `pgadmin-data/` directory



**Now, start your services with `docker compose` command:**

```shell
# Run containered services
docker compose up -d

# Check your services status
docker compose ps

# And if you wish to stop them
docker compose down
```

**For troubleshooting container issues, use logs:**

```shell
# To monitor a specific service's logs (without following)
docker compose logs ServiceName
# And if you want to use following option
docker compose logs -f ServiceName
```

Note: If you don't provide `ServiceName` argument, it will return all services logs

Finally, open `https://yourserverip:PGADMIN_PORT` in your browser and user credentials those provided in `pgadmin_env` file and if you want to register your postgres server in pgAdmin, use credetials provided in `postgres_env` file.

Enjoy ;)
