## Example of a PHP web application using Docker

#### Important links
https://gist.github.com/thaJeztah/8d0e901bd21329d80cf2

https://stackoverflow.com/questions/41984399/denied-requested-access-to-the-resource-is-denied-docker

https://runnable.com/blog/9-common-dockerfile-mistakes

https://mybrainimage.wordpress.com/2017/02/05/docker-change-port-mapping-for-an-existing-container/

If internet is not working on container
https://stackoverflow.com/questions/24991136/docker-build-could-not-resolve-archive-ubuntu-com-apt-get-fails-to-install-a

#### How does it work?

#### Some Tweaks
strace docker pull busybox

```bash
# build the docker image
docker build -t php-docker-apache-example .
# to run docker container from Dockerfile, here 'freeswitch' will be the container's name
docker build -t "freeswitch:dockerfile" .

# run the docker container on this machine. Expose its internal
# port 80(Container's) to this machine's port 8080(Host's)
docker run -d -p 8080:80 php-docker-apache-example

#For astpp-freeswitch container
docker run --restart=always -i -t -p 4001:8021 -p 5001:5001 -p 5001:5001/udp -p 17000:17000/udp -p 17001:17001/udp -p 17002:17002/udp -p 17003:17003/udp -p 17004:17004/udp -p 17005:17005/udp -p 17006:17006/udp -p 17007:17007/udp -p 17008:17008/udp -p 17009:17009/udp --name fs_ketan <image_id> /bin/bash

==================
#ssh or sftp to container
apt-get install ssh
echo 'root:my_root_pwd' | chpasswd

/etc/ssh/sshd_config

PermitRootLogin without-password
change to
PermitRootLogin yes

/usr/sbin/sshd -D
==================

#To create symbolic link from host to container
    -v /home/test/:/home/test
#To not start container at host restart
 docker update --restart=no fs_ketan
 #To start container at host restart
  docker update --restart=always fs_ketan

docker run --restart=always -i -t -p 4004:8021 -p 5004:5004 -p 5004:5004/udp -p 18001-18500:18001-18500/udp --name fs_ankit fd257891580e /bin/bash

sudo iptables -A DOCKER -t nat -p udp -m udp ! -i docker0 --dport 16384:32767 -j DNAT --to-destination 172.17.0.2:16384-32767
sudo iptables -A DOCKER -p udp -m udp -d 172.17.0.2/32 ! -i docker0 -o docker0 --dport 16384:32767 -j ACCEPT
sudo iptables -A POSTROUTING -t nat -p udp -m udp -s 172.17.0.2/32 -d 172.17.0.2/32 --dport 16384:32767 -j MASQUERADE

#IMP
By typing ctrl+p and ctrl+q after each other, you turn interactive mode to daemon mode, which keeps the container running but frees up your terminal.

#docker user access change
groupadd docker
gpasswd -a inextrix docker
chmod 777 /var/run/docker.sock

# How to copy docker images from one host to another
You will need to save the docker image as a tar file:

docker save -o <path for generated tar file> <image name>

Then copy your image to a new system with regular file transfer tools such as cp or scp. After that you will have to load the image into docker:

docker load -i <path to image tar file>

# to connect terminal to particular image/container on front
docker run -i -t --entrypoint /bin/bash <imageID>
sudo docker exec -i -t <container_name_or_id> /bin/bash
# to run the container with big resolution
docker exec -e COLUMNS=$COLUMNS -e LINES=$LINES -e TERM=$TERM -i -t f992c5b5e2ca /bin/bash

# Get the list of exited container
docker ps --filter "status=exited"

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

# How to copy docker images from one host to another
You will need to save the docker image as a tar file:
docker save -o <path for generated tar file> <image name>

Then copy your image to a new system with regular file transfer tools such as cp or scp. After that you will have to load the image into docker:
docker load -i <path to image tar file>


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
