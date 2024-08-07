What is Docker and why it is used?

Docker is **a software platform that allows you to build, test, and deploy applications quickly**. Docker packages software into standardized units called containers that have everything the software needs to run including libraries, system tools, code, and runtime.

- Docker is a software platform for developing, shipping, and running applications. 
- It provides a way to package application and their dependencies into lightweight containers
	- which can then be easily deployed across different environments, such as development, testing and production.

- Docker uses a client-server architecture,
- where the Docker Engine serves as the server, 
- Server: for managing containers and images, 
- while the Docker client, 
- Client: provides a command-line interface for interacting with Docker Engine.

#### Hypervisor
- A hypervisor is a software that you can use to run multiple virtual machines on a single physical machine. 
- Every virtual machine has its own operating system and applications. 
- The hypervisor allocates the underlying physical computing resources such as CPU and memory to individual virtual machines as required.

#### Virtualization
-   Virtualization is a technology that enables the creation of virtual instances of computer hardware, software, storage devices, and network resources. 
- These virtual instances, known as virtual machines (VMs) or virtual environments, operate independently from the physical hardware, allowing multiple operating systems and applications to run on a single physical machine simultaneously.

#### Key benefits of Docker

1. **Consistency**: Containers ensure that an application behaves consistently across different environments, 
	1. reducing the chances of issues arising due to differences in the underlying infrastructure.
    
2. **Isolation**: Containers provide isolation for applications, 
	1. allowing them to run independently without interfering with each other or the host system.
    
3. **Portability**: Docker containers can be easily moved and deployed across different platforms and environments, 
	1. making it easier to build, test, and deploy applications.
    
4. **Resource Efficiency**: Containers are lightweight and share the host system's kernel, resulting in lower overhead compared to traditional virtual machines. 
	1. Docker **uses fewer resources compared to traditional virtual machines.** 
	2. Because containers share the same underlying operating system kernel, 
		1. they are much lighter and require less disk space and RAM. 
		2. This allows more applications to run on a single machine, which **saves hardware costs and eases resource management.**
5. **Faster Development and Deployment**: Docker simplifies the application development and deployment process. 
	1. Containers **allow developers to work in local environments identical to production environments, 
		1. which reduces compatibility issues and speeds up the development cycle.**
6. **Scalability**: Docker facilitates application scalability. 
	1. **You can create multiple instances of a container and distribute the workload efficiently** using container orchestration tools such as Kubernetes

### Kernel

It decides which process should be allocated to processor to execute and which process should be kept in main memory to execute. It basically acts as an interface between user applications and hardware. The major aim of kernel is to manage communication between software i.e. user-level applications and hardware i.e., CPU and disk memory.   
  
**Objectives of Kernel :**   
 

- To establish communication between user level application and hardware. 
- To decide state of incoming processes.  
- To control disk management.  
- To control memory management. 
- To control task management.
## Hands on
### Basic Commands

```sh
$ docker version

$ docker login

$ docker logout

$ docker ps # list of running containers

$ docker ps -a # list all containers

$ docker images # list the images

$ docker image ls # list the images

$ docker run nginx # run or spin up the container with the given image

$ docker run -d -p 8080:80 nginx

$ docker run -d -p 8081:80 --name my-nginx nginx

$ docker --help # more information or list of commands you can use

$ docker rm <id/name> <id/name> .... # to remove multiple containers, only that are stopped

$ docker rmi <image-name> <image-name> .... # to remove the images

```


### CLI Process Monitoring

```sh
$ docker top <container-name/id> # display the running processes of a container

$ docker inspect <container-name/id> # displays the metadata about the container (startup config, volumes, netwroking, etc)

$ docker stats # performance stats for all containers

$ docker stats <container-name/id> # persormance stats for particular container

$ docker logs <container-name/id> #  allows you to view the logs generated by a specific Docker container.

$ docker logs --tail <n> <container-name/id> # display the last n lines of the logs
```

### Getting a Shell Inside Containers

```sh
$ docker run -it
```
		Starts new container interactively

```sh
$ docker exec -it
```
		Run additional command in existing container

> -it 
> -i :  interactive
> 	keep session open to receive terminal input
> -t :  pseudo-tty
> 	simulates a real terminal, like what SSH does
> bash shell :  if run with -it, it will give you a terminal inside the running container

### Different Linux distros in containers

```sh
$ docker run -it --name proxy nginx bash
```

```sh
$ docker exec -it mysql bash
```

---
---
# Live Examples
---
## Change the Root file of the Nginx Container

#### Steps:
1. Create & Run the container
	```sh
	$ docker run -d -p 8080:80 --name ng-customize nginx
```

2. Getting a Shell Inside a container
		Two ways:
			1. Using GUI
			2. Using CLI
```sh
$ docker exec -it <container-name> bash
```

3. Find the file
		*nginx path: /usr/share/nginx/html/index.html*
		*httpd path: /usr/local/apache2/htdocs/index.html*
		
4. Change the index.html file
5. Save the file
6. Reload the page on your browser



## Spin up the mysql Container
#### Steps
1. Pull the image

```sh
$ docker pull mysql:latest
```

2. Run the container

```sh
$ docker run -d -p 3306:3306 --name mysqldb-server -e MYSQL_ROOT_PASSWORD=test123 mysql
```

3. Go inside the container and access the shell

```sh
$ docker exec -it mysqldb-server bash
```

4. Connect to your database

```sh
$ mysql -u root -p
```

5. Run SQL commands

```sql
show databases;

create databae sampleDb;

use sampleDb;

show tables;

create table sampleTbl (id int, name varchar(20));

show tables;

insert into sampleTbl values (1, 'Tom');

select * from sampleTbl;
```







USING THE env variable MYSQL_RANDOM_ROOT_PASSWORD=yes

```SH
$ sudo docker logs mysql-db1 2>&1 | grep "GENERATED ROOT PASSWORD"
```




## How to build Docker Image

#### Steps
1. Create Dockerfile

```Dockerfile
# Use the official .NET Core SDK as a parent image  
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build  
WORKDIR /app  
  
# Copy the project file and restore any dependencies (use .csproj for the project name)  
COPY *.csproj ./  
RUN dotnet restore  
  
# Copy the rest of the application code  
COPY . .  
  
# Publish the application  
RUN dotnet publish -c Release -o out  
  
# Build the runtime image  
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS runtime  
WORKDIR /app  
COPY --from=build /app/out ./   sa
  
# Expose the port your application will run on  
EXPOSE 80  
  
# Start the application  
ENTRYPOINT ["dotnet", "NET-Core-Web-API-Docker-Demo.dll"]
```


2. Build the image using the following command

```sh
$ docker build -t <name-image> .
```

3. Spin up the container using the following command

```sh
$ docker run -d -p 5001:80 --name <name-you-want-to-give-to-the-container> <your-image-name>
```


## Push the Image to the Registry
Steps:
1. Build the Image
2. Tag the Image

```sh
$ docker image tag <image-name> <server-name>/<image-name>
```

3. Push the image

```sh
$ docker image push <server-name>/<image-name>
```


---


## Docker Compose

Docker Compose is a tool for defining and running multi-container applications. It is the key to unlocking a streamlined and efficient development and deployment experience.
#### Create Docker Compose File
Steps:
1. Create File 

```yml
version: '1'
services:
  TestServiceAPI:
    image: "kanhaiyadev/test-api"
    build:
      context: /Test-WebAPI/Test-WebAPI/
      dockerfile: Dockerfile
    ports:
      - "5001:80"
      - 
  SampleServiceAPI:
    image: "kanhaiyadev/sample-api"
    build:
      context: /Sample-WebAPI/Sample-WebAPI/Sample-webAPI/
      dockerfile: Dockerfile
    ports:
      - "5002:80"
```


2. Run the command

```sh
$docker compose up -d
```

#### Other Commands
```sh
$ docker-compose start
$ docker-compose stop
```

```sh
$ docker-compose pause
$ docker-compose unpause
```

```sh
$ docker-compose ps
$ docker-compose up
$ docker-compose down
```


https://docs.docker.com/compose/reference/