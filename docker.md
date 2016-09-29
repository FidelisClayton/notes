# Docker

### Install

```
 $: curl -sSl https://get.docker.com | sh
```

### Starting Docker

```
 $: /etc/init.d/docker start
```

### Listing active Dockers

```
 $: docker ps
```
If you get this message: 
  docker: Cannot connect to the Docker daemon. Is the docker daemon running on this host?.
  
Try to run the command using sudo.

### Installing a new image

```
 $: docker run -i -t ubuntu:14.04 /bin/bash
```

This will install the Ubuntu 14.04 image and start with the bash process;

Now if we run **"uname -a"**, we'll see the current data about our Docker container. Something like:

```
Linux eeb39f31e7bb 4.4.0-38-generic #57-Ubuntu SMP Tue Sep 6 15:42:33 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
```

### Lefting the Container

Press CTRL + P + Q to close the docker and it will stay active. So run "docker ps" to see the images that are running on docker.

```
$: sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
eeb39f31e7bb        ubuntu:14.04        "/bin/bash"         3 minutes ago       Up 3 minutes                            big_easley
```

### Backing to our container

Run **docker attach <your_container_id>**

```
$: sudo docker attach eeb39f31e7bb
root@eeb39f31e7bb:/#     
```

Now we're back to our Ubuntu 14.04 Container.

### Logging out

It's very simple, just press Ctrl + D. To probe this, run:

```
$: sudo docker ps
```
And wi'll get:

```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

Yep, no containers running.

## Now... Let's play a little bit

What now? Let's install NGinx to up a web server.

Run:

```
$: docker run -i -t -p 8080:80 ubuntu:14.04 /bin/bash
```

It will start our container at the port 8080 of our physical machine and port 80 on our container.
So now run the following commands to install nginx:

```
root@1d44f2426e7e:/# apt-get update
root@1d44f2426e7e:/# apt-get install nginx
```

Now lets start the nginx server: 
```
root@1d44f2426e7e:/# /etc/init.d/nginx start
```

And:
```
root@1d44f2426e7e:/# ps -ef
```

To show the active process list:

```
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 00:33 ?        00:00:00 /bin/bash
root       508     1  0 00:39 ?        00:00:00 nginx: master process /usr/sbin/nginx
www-data   509   508  0 00:39 ?        00:00:00 nginx: worker process
www-data   510   508  0 00:39 ?        00:00:00 nginx: worker process
www-data   511   508  0 00:39 ?        00:00:00 nginx: worker process
www-data   512   508  0 00:39 ?        00:00:00 nginx: worker process
root       513     1  0 00:41 ?        00:00:00 ps -ef
```

How we can see, now our container is running the bash and nginx. Now type _localhost:8080_ on your browser and you'll see the nginx default page.

## Saving our Docker container

Just run "docker commit <your_container_id> container_name:container_version". So:

```
$: sudo docker commit 1d44f2426e7e ubuntu-nginx:1.0
```

**WOW, can we versioning our container???** Yep, exactly that.

## Listing our Docker images

Just run "**docker images**" and wi'll get:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu-nginx        1.0                 d887803964b7        2 minutes ago       228.3 MB
ubuntu              14.04               f2d8ce9fa988        2 days ago          187.9 MB
```

## Starting our container image

Just run "**docker run -i -t -p 8080:80 ubuntu-nginx**":

```
$: sudo docker run -i -t -p 8080:80 ubuntu-nginx:1.0 /bin/bash
```

And we're back!

