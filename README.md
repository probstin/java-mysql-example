# Spring Boot with a MySQL Container

## Running the Application

- `git clone && cd java-mysql-example`
- `cp mysql.env.EXAMPLE mysql.env`
- Specify a `MYSQL_PASSWORD` for the created user: `example_user`
- Inspect te `docker/mysql/init.sql` file to see where the `example_db` is created and the `example_user` is granted permission.
- Open a terminal window: `docker-compose up` to start the MySQL container
- Open another terminal window: `./mvnw spring-boot:run -Dspring-boot.run.arguments="--spring.datasource.url=jdbc:mysql://127.0.0.1:3307/example_db --spring.datasource.username=example_user --spring.datasource.password=YOUR_PASSWORD_HERE"`

## The Configuration

### `docker-compose.yml`
Contains a MySQL container that exposes port `3307` and forwards to `3306` internally, where the MySQL server is running. `3307` is used as the exposed port in the event you have a local running instance of a MySQL server - more than likely on `3306`.

The `db` service within defines an `env_file`, titled `mysql.env`, that contains the sensitive environment variables captured in the `mysql.env.EXAMPLE` file. These variables will get loaded and applied by the MySQL container at runtime. See the [official image documentation](https://hub.docker.com/_/mysql) for more information and a complete list of environment variables.

The `db` service also defines a `volume` that loads in an entrypoint directory `./docker/mysql` that contains an `init.sql` file that initializes an example database. The folder gets mounted to the container to `/docker-entrypoint-initdb.d/` where it is then executed on startup.

### `mysql.env`

Contains the sensitive environment variables captured in the `mysql.env.EXAMPLE` file. These variables will get loaded and applied by the MySQL container at runtime. See the [official image documentation](https://hub.docker.com/_/mysql) for more information and a complete list of environment variables. This file has been added to the `.gitignore` file and should not be committed!

### `Spring Datasource Properties`

The `application.properties` file has been removed here to promote good hygeine when handling sensitive values. Instead, we can specify these at runtime by overriding the spring properties via the Maven Plugin command `-Dspring-boot.run.arguments`:

```bash
./mvnw spring-boot:run \
-Dspring-boot.run.arguments="--spring.datasource.url=jdbc:mysql://127.0.0.1:3307/example_db --spring.datasource.username=example_user --spring.datasource.password=YOUR_PASSWORD_HERE"
```

In a real application, you might look to parameterize the `application.properties` file.

### `init.sql`

The `docker/mysql/init.sql` file contains SQL that is run by the MySQL container on startup. In this example, we are simply creating a database for the application to connect to and then granting the `MYSQL_USER`, defined in the `mysql.env` file, access to that database.