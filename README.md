RStudio in a Docker Container
=============================

## What is this?

This is a Docker image loaded with software for performing microbiome
analysis on amplicon (e.g. 16S rRNA).  It also contains RStudio
server, the knitr and Rmarkdown libraries necessary for knitting
Rmarkdown documents, and enough of TeX to allow knitr to generate PDF output.


## How to run

### Basic
The following command will download the image from Docker Hub and run
it.

Run using the default password from the Dockerfile build script:
```
docker run --name ibiem \
	-d -p 8787:8787 \
	-e USERPASS=badpa55word \
	-t ibiem/ibiem2018
```

After running the command, open http://localhost:8787 in a web
browser.  When you get the RStudio login, the username is “guest” and
the password is “badpa55word”.

### More secure
The basic command above is a good test, but there are some ways to
make run the image a bit more securely.

First, follow [these instructions](#shutdown-the-container) to
shutdown the container you just started.

#### Better password
Instead of “badpa55word” substitute a better password in the `-e
USERPASS=badpa55word` part of the command.  You will then use this at
the RStudio login. For example:

```
docker run --name ibiem \
	-d -p 8787:8787 \
	-e USERPASS=ThisIsA8etterPa55wordMaybe \
	-t ibiem/ibiem2018
```

#### Restrict access to local computer
With the [basic run command](#basic), anyone on the internet can
connect to your container with a web browser (and the password).  You
can change the command to restrict it so that only a browser running
on the same computer can connect using this command:

```
docker run --name ibiem \
	-d -p 127.0.0.1\:8787\:8787 \
	-e USERPASS=ThisIsA8etterPa55wordMaybe \
	-t ibiem/ibiem2018
```

Of course this is only appropriate if you are running the docker image
and the web browser you are using to access it on the same computer.

### Access files on host computer
By default Docker containers can’t access anything on the host
computer, its basically a “What happens in the docker container, stays
in the docker container” situation. This means that by default
anything that you do in the docker container disappears when you shut
it down.  This is not the end of the world if you do all your work in
a git repo within the container and make sure to regularly commit and
push, but it can be inconvenient.  The common solution to this problem
is to allow the container to connect a directory within the container to a directory on the host computer, so anything you save to the directory in the container is really being saved to the host computer.  You can do this with by adding `-v HOST_COMPUTER_DIRECTORY:CONTAINER_DIRECTORY` to the docker run command, so for example the following commands would make a subdirectory called “platypus_project” in the home directory on your host computer and connect it to a directory called “workspace” within the container.


```
docker run --name ibiem \
	-d -p 127.0.0.1\:8787\:8787 \
	-e USERPASS=ThisIsA8etterPa55wordMaybe \
	-v $HOME/platypus_project:/home/guest/workspace \
	-t ibiem/ibiem2018
```

Try to run this, then save a file in /home/guest/workspace within the container, then look in “platypus_project” in the home directory of your computer to see if the file showed up.


## Shutdown the container
You can check what containers are running with the command
`docker ps -a`, which should give you something like the following
(CONTAINER ID and STATUS will be different).

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS                    NAMES
746fa56d80ff        ibiem/ibiem2018     "/usr/bin/supervisord"   6 minutes ago       Up 6 minutes               0.0.0.0:8787->8787/tcp   ibiem
```

If you ran the container using any of the above commands, you should
be able to shut it down with `docker rm -f ibiem`, then you can
confirm with `docker ps -a`, which should show no containers running now.
