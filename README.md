## Example of a PHP web application using Docker


#### How does it work?

```bash
# build the docker image
docker build -t php-docker-apache-example .

# run the docker container on this machine. Expose its internal
# port 80 to this machine's port 8080
docker run -d -p 8080:80 php-docker-apache-example

# to connect terminal to particular image/container on front
docker run -i -t --entrypoint /bin/bash <imageID>

# to connect terminal of particular already running container in backend
docker exec -i -t <Container_ID> /bin/bash

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

# Check logs
docker logs <Container_ID>

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
