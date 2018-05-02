## Example of a PHP web application using Docker

#### Important links
https://gist.github.com/thaJeztah/8d0e901bd21329d80cf2

https://stackoverflow.com/questions/41984399/denied-requested-access-to-the-resource-is-denied-docker

#### How does it work?

```bash
# build the docker image
docker build -t php-docker-apache-example .
# to run docker container from Dockerfile, here 'freeswitch' will be the container's name
docker build -t "freeswitch:dockerfile" .

# run the docker container on this machine. Expose its internal
# port 80(Container's) to this machine's port 8080(Host's)
docker run -d -p 8080:80 php-docker-apache-example

#IMP
By typing ctrl+p and ctrl+q after each other, you turn interactive mode to daemon mode, which keeps the container running but frees up your terminal.

# to connect terminal to particular image/container on front
docker run -i -t --entrypoint /bin/bash <imageID>
sudo docker exec -i -t <container_name_or_id> /bin/bash

#This will start all container which are in exited state.
docker start $(docker ps -a -q --filter "status=exited")

# to connect terminal of particular already running container in backend
docker exec -i -t <Container_ID> /bin/bash

#commit and push image to docker hub
first pull from source and run the container
Commit your changes with adding tag to it
docker commit --message="Will change freeswith as per ASTPP" freeswitch freeswitch_astpp:280418
[Second way: then tag it
docker tag firstimage YOUR_DOCKERHUB_NAME/firstimage]
then login into your docker account
docker login
then push the container as your account's repo
docker push devangn/freeswitch_astpp:280418

# to run container in backgroud
docker run -t -d <imageID>

# to get the list of images
docker images

# to get the list of containes
docker container ls

# to set the static ip to container https://stackoverflow.com/questions/25529386/how-can-i-set-a-static-ip-address-in-a-docker-container
docker network create --subnet=192.168.1.0/24 mynetwork
docker run -t -d --net mynetwork --ip 192.168.1.122 <Image_ID>

# start containers from docker-compose in backgroup
docker-compose up -d

# stop all running containers
docker stop $(docker ps -aq)

# Remove all exited containers
docker rm $(docker ps -a -f status=exited -q)

#remove image
docker rmi <image_id>

# Check logs
docker logs <Container_ID>

# Check the status for container under composer
docker-compose ps

```

And you can see the result here:
![screenshot](https://raw.githubusercontent.com/fuhrysteve/php-docker-apache-example/master/example.jpg)

```bash
# to spin up additional containers on different ports, you might
# do something like this:
docker run -d -p 5000:80 php-docker-apache-example
docker run -d -p 5001:80 php-docker-apache-example
docker run -d -p 5002:80 php-docker-apache-example
docker run -d -p 5003:80 php-docker-apache-example
docker run -d -p 5004:80 php-docker-apache-example
docker run -d -p 5005:80 php-docker-apache-example
docker run -d -p 5006:80 php-docker-apache-example
docker run -d -p 5007:80 php-docker-apache-example
```
Now you have 8 concurrent webservers running, all ready to serve
traffic on 8 different ports. A load balancer would typically
choose which container to send a request to based on load /
availability / etc.

If you wanted to deploy a new version of your software but are nervous
of breaking something, you could spin up new containers and leave the
old ones running. If anything bad happens, switching back to the old
containers is quick and reliable.


#### More information

* The PHP application code goes in `myapp/`
* The first line of the Dockerfile `FROM php:7.0-apache` means "use
  the official maintained image (which happens to be debian based)
  with php version 7 and apache installed on it". When you rebuild your
  images, you get any security updates along with it for free.
* Developers are typically responsible for creating and maintaining 
  the Dockerfile and any dependencies their code makes use of (for
  example, if they want to use ImageMagick to convert images to
  thumbnails, they will have to put it in the Dockerfile, otherwise
  their code would break).
* Applications should avoid writing to the filesystem (when uploading
  files, for instance) and prefer to use object storage, such as
  Amazon S3 instead. The reason for this is that it is a maintenance
  headache. It is definitely possible. It is usually more annoying
  than alternatives.
* In development, the developer would likely also use a docker container
  for MySQL / similar persistent data stores. In production, their Docker
  container would likely connect to a different (managed) database.
