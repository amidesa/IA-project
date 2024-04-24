## Kanban Application

Hey everyone, In this blog post we will be deploying a full stack web application using Docker. The application will be split into three microservices.

   1. Frontend Container of ReactJs
    2.Backend Container of NodeJs
    3.Database Container of MongoDB

Before we start with the deployment, let’s understand what Docker is and why we need it. Docker is a platform that allows you to develop, test, and deploy applications in containers. Containers are lightweight, standalone, and executable packages of software that include everything needed to run an application: code, runtime, system tools, system libraries, and settings. Containers are isolated from each other and from the underlying host, which means that they can run the same application on different environments without any changes. This makes it easy to deploy applications in a consistent and reproducible way.

Docker also provides tools for managing containers, such as Docker Compose, which allows you to define and run multi-container applications. In this blog, we will be using Docker Compose to define and run our full stack web application. Let’s start

1. Create a full stack application or use an existing one built on ReactJs, NodeJs and MongoDB.Permalink

I have used an application of my own which you can clone from Stock Suggestion App or use any existing application you have. Just make sure it has the same tech-stack.

This is a simple implementation of a Kanban Board, a tool that helps visualize and manage work. Originally it was first created in Toyota automotive, but nowadays it's widely used in software development.

A Kanban Board is usually made of 3 columns - *TODO*, *InProgres*s & *Done*. In each column there are Post-it notes that represents task and their status.

As already stated this project is an implementation of such board and made of 3 separate Docker containers that holds:

- PostgreSQL database
- Java backend (Spring Boot)
- Angular frontend

The entry point for a user is a website which is available under the address: **http://localhost:4200/**

![Kanban](https://github.com/wkrzywiec/kanban-board/blob/master/assets/kanban.gif)

More information about this project you can found in blog post: https://medium.com/@wkrzywiec/how-to-run-database-backend-and-frontend-in-a-single-click-with-docker-compose-4bcda66f6de
 ---
 2. Create a repository on Docker Hub to store your Docker images.Permalink

Go to Docker Hub and create a new repository to store your Docker images. You can create a public or private repository depending on your requirements. Once you have created the repository, note down the repository name as we will need it later to push our Docker images to the repository.

3. Create a Dockerfile for each of the services.Permalink

Dockerfile is a text file that contains a set of instructions that are used to build a Docker image. The Docker image is a lightweight, standalone, and executable package of software that includes everything needed to run an application. The Dockerfile contains instructions to build the image, such as the base image, working directory, dependencies, and commands to run the application.

4.Now we will build the Docker image from the created Dockerfile and push it to the Docker Hub repository, follow the below steps carefully

    Build the Docker image using the following command:

  docker build -t <DockerHubUsername>/<RepositoryName>:client .

  5.Replace <DockerHubUsername> with your Docker Hub username and <RepositoryName> with the repository name of the created repository in step 2.

This command will build the Docker image for the ReactJs server using the Dockerfile in the client folder.

   == Lets check if the image is created successfully by running the following command:

     docker images

![image](https://github.com/amidesa/IA-project/assets/166305750/d792cefb-1bf2-4788-aa8f-0dd7efe285b9)

6.This command will list all the Docker images on your system. You should see the Docker image for the ReactJs server in the list.

    Now we will see if the image is running successfully by running the following command:

    docker run -p 3000:3000 <DockerHubUsername>/<RepositoryName>:client

    

### Prerequisites

In order to run this application you need to install two tools: **Docker** & **Docker Compose**.

Instructions how to install **Docker** on [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/), [Windows](https://docs.docker.com/docker-for-windows/install/), [Mac](https://docs.docker.com/docker-for-mac/install/).

**Docker Compose** is already included in installation packs for *Windows* and *Mac*, so only Ubuntu users needs to follow [this instructions](https://docs.docker.com/compose/install/).


### How to run it?

The entire application can be run with a single command on a terminal:

```
$ docker-compose up -d
```

If you want to stop it, use the following command:

```
$ docker-compose down
```

---

#### kanban-postgres (Database)

PostgreSQL database contains only single schema with two tables - kanban
and task table.

After running the app it can be accessible using these connectors:

- Host: *localhost*
- Database: *kanban*
- User: *kanban*
- Password: *kanban*


Like other parts of application Postgres database is containerized and
the definition of its Docker container can be found in
*docker-compose.yml* file.

```yml
kanban-postgres:
    image: "postgres:9.6-alpine"
    container_name: kanban-postgres
    volumes:
      - kanban-data:/var/lib/postgresql/data
    ports:
      - 5432:5432
    environment:
      - POSTGRES_DB:kanban
      - POSTGRES_USER:kanban
      - POSTGRES_PASSWORD:kanban
```

#### kanban-app (REST API)

This is a Spring Boot (Java) based application that connects with a
database that and expose the REST endpoints that can be consumed by
frontend. It supports multiple HTTP REST methods like GET, POST, PUT and
DELETE for two resources - kanban & task.

Full list of available REST endpoints could be found in Swagger UI,
which could be called using link: **http://localhost:8080/api/swagger-ui.html**


![swagger-ui](https://github.com/wkrzywiec/kanban-board/blob/master/assets/swagger.png)


This app is also put in Docker container and its definition can be found
in a file *kanban-app/Dockerfile*. 



#### kanban-ui (Frontend)

This is a real endpoint for a user where they can manipulate their
kanbans and tasks. It consumes the REST API endpoints provided by
*kanban-app*.

It can be entered using link: **http://localhost:4200/**
