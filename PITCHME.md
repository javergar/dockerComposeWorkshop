![Docker-compose](docker_compose.png)
---

Docker Compose. Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a Compose file to configure your application's services. Then, using a single command, you create and start all the services from your configuration.

---

For this tutorial we need to clone the following repository:

```
git clone git@bitbucket.org:ansarada/docker-compose-workshop.git
cd docker-compose-workshop/aspnetcore-mssql/
```

---

The Docker Compose representation of the two-container application
for our tutorial looks as follow:

```docker
version: "3"
services:
    web:
        build: .
        ports:
            - "8000:80"
        depends_on:
            - db
    db:
        image: "microsoft/mssql-server-linux"
        environment:
            SA_PASSWORD: "${SA_PASSWORD}"
            ACCEPT_EULA: "Y"
```
---

To launch our application, we simply change to the folder that contains your `docker-compose.yml` file and run the following:

```
docker-compose up
```

After executing we can see that the command did the following:

1. Created a network called `aspnetcoremssql_default` with the default driver
2. Builded two containers, one called `aspnetcoremssql_db_1` and the second `aspnetcoremssql_web_1`. 
3. Launched the two containers

---

## Structure of docker-compose.yml

The docker compose file is composed of different sections.

The first section specifies which version of the Docker Compose definition language we are using; in our case, as we are running a recent version of Docker and Docker Compose, we are using version 3.

The next section `services` is where our containers are defined; this section is the services section;
in our example, we defined two containers `web` and `db`.

```docker
version: "3"
services:
    web:
        build: .
        ports:
            - "8000:80"
        depends_on:
            - db
    db:
        image: "microsoft/mssql-server-linux"
        environment:
            SA_PASSWORD: "${SA_PASSWORD}"
            ACCEPT_EULA: "Y"
```
---

The syntax for defining the service is close to how you would launch a container using  `docker run`.

1. `build`:The build instruction here tells Docker Compose to build a container using the Dockerfile, which can be found in the current `.` folder.

2. `image`: This tells Docker Compose which image to download and use. This does not exist as an option when running docker container run on the command line as you can only run a single container; as we have seen in previous chapters, the image is always defined toward the end of the command without the need of a flag being passed.

3. `volume`: This is the equivalent of the `-- volume flag`, but it can accept multiple volumes.

4. `depends_on`: This would never work as a docker container run invocation because the command is only targeting a single container. When it comes to Docker Compose, depends_on is used to help build some logic into the order your containers are launched in. For example, only launch container `B` when container `A` has successfully started.

5. `ports`: This is basically the --publish flag, which accepts a list of ports.

---

## Docker Compose commands

### Up and ps

The first one is `docker-compose up`, if we are the flag `-d`
we can run the containers in detached mode.

```
docker-compose up -d
```

After we are returned to the terminal we can verify that the containers 
are running by using the `ps` command.

```
docker-compose ps
``` 

### Config

Running `docker-compose config` validates the `docker-compose.yml` file.
If there are no issues it prints the Docker Compose YAML file to screen.

### Pull, build and create

``` docker-compose pull ```
Reads your Docker Compose YAML file and pull any of the images it finds.

```docker-compose build ```
Triggers a build if there are updates to any of the Dockerfiles used to originally build your images.

```docker-compose create```
Creates but not launch the containers

### Start, stop, restart, pause, and unpause

This commands work exactly in the same way as their docker container counterparts, the only difference being they effect change on all of the containers.

It is possible to target a single service by passing its name; for example, to pause and unpause the db service we would run:

```
docker-compose pause db
docker-compose unpause db
```

### Top, logs, and events

The next three commands all give us feedback on what is happening within our running containers and Docker Compose.

```
docker-compose top
```

If you would like to see just one of the services, you simply have to pass its name when running the command.

```
docker-compose top db
```

```
docker-compose logs
```
You can pass flags such as -f or --follow to keep the stream flowing until you press Ctrl + C. Also, you can stream the logs for a single service by appending its name to the end of your command.

```
docker-compose events
```

### Exec and run

These two commands run similar to their docker container equivalents.

For instance we can run the following command to access `sqlcmd` on the `db` service. 

```
docker-compose exec db /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Qwerty12
```

### Scale

The scale command will take the service you pass the command and scale it to the number you define; for example, to add more `web` containers I just need to run:

```
docker-compose up -d --scale web=3
```

Unfortunatelly: 
[Port ranges are not working at the moment](https://github.com/docker/compose/pull/4649)

### Kill, rm, down

```
docker-compose kill 
```
Be careful when running this as it does not wait for containers to gracefully stop like when running 

``` 
docker-compose rm 
```
Removes any containers with the state of exited

``` 
docker-compose down 
```
removes the containers and the networks created when running docker-compose up.

