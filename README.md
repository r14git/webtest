docker-nginx-busybox (~ 8 MB image size)
====================
This is a "Hello World" example on how to build docker images (manually and automated). 

As target of our hello world examle, I have chosen a tiny docker image for nginx built on top of busybox forked from https://github.com/jimmidyson/docker-nginx-busybox. A successful build will result in an image of ~8 MB (i.e. a perfect hello world size).

1. We describe on how to perform a manual build from Dockerfile and how to test the image. 
2. We describe on how to automatically build a Docker image from a Git repository.

If you are searching for more professional nginx images with prepared https support, check out the official nginx image on https://hub.docker.com/_/nginx/, which is larger as this image (~52 MB). If it must be smaller, https://hub.docker.com/r/connexiolabs/alpine-nginx/ with ~27 MB might be an alternative as well.

Manually build a docker image
-----------------------------
(Status: tested successfully)

On the docker host, perform the command:

    docker build --tag=nginx-busybox:latest .

Verify that the image has been created:

    docker images
    
should lead to an output similar to:

    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    nginx-busybox       latest              5c21ee35c67c        8 hours ago         7.898 MB

Start Container from the above created image
--------------------------------------------
On the docker host, perform the command:

    docker run -d -p 80:80 nginx-busybox

Verify that the container has been created and is up and running:

    docker ps
    CONTAINER ID        IMAGE                  COMMAND             CREATED             STATUS              PORTS                         NAMES
    a8614bead979        nginx-busybox:latest   "/usr/sbin/nginx"   4 seconds ago       Up 3 seconds        0.0.0.0:80->80/tcp, 443/tcp   stoic_mestorf

Connect to the NginX reverse Proxy locally
------------------------------------------
On the docker host, the commands

    export http_proxy=
    curl localhost
    
Should print the content of index.html to the screen:

    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx on busybox!</title>
    ...
    <p><em>Thank you for using nginx.</em></p>
    </body>
    </html>

Connect to the NginX reverse Proxy per Browser
----------------------------------------------
Linux:
If you start a browser on the Linux docker host, use the URL 

    http://localhost
    
Windows or iOS:
On boot2docker hosts (Windows and iOS), find your docker host's IP address by issuing the command 
    
    boot2docker ip
     
on the boot2docker start commandline window. Usually, this is a Virtualbox host-only network and is reachable from the Windows or iOS host only, but not from the outside world.

On Windows or iOS using a custom Linux Virtualbox VM as docker host (as we recommend since the installation of boot2docker is a nightmare sometimes), networking can be more tricky:

On the Linux docker host, issue the command

    ifconfig
    
The interface docker0 and eth0 with IP address 10.0.2.15 are not reachable from the Windows or iOS host. Therefore, we recommend to provision the custom Linux Virtualbox VM with an additional interface eth1 with a host-only (if it does not need to be reached from outside the Windows or iOS host) or bridged network. In this case the output of ifconfig will look something like  

    core@core-01 ~/docker-nginx-busybox $ ifconfig
    docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 10.1.42.1  netmask 255.255.0.0  broadcast 0.0.0.0              <--- not reachable from the outside!!
            ...
    
    eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255         <--- not reachable from the outside!!
            ...
    
    eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 172.17.8.101  netmask 255.255.255.0  broadcast 172.17.8.255    <--- host-only network -> only reachable from the Virtualbox host
            ...

From the Windows or iOS host, open an Internet browser and put an URL like

    http://<IP address of eth1>

to the URL field; in the example above, this is http://172.17.8.101.

Automatically build a Docker Image on Docker Hub from this Repository
---------------------------------------------------------------------
(Status: tested successfully)

Sign up for Github and Docker Hub, if not already done. 

Fork this git repository (button on the upper right), if not already done.

On the Git Hub repository project home (https://github.com/<yourname>/docker-nginx-busybox), goto -> Settings (on the right) -> Webhooks & Services -> Add Service -> choose Docker -> enter your Docker Hub password and confirm

On Docker Hub Home, left of your username -> Create -> Create Automated Build -> Choose your repository -> Enter short description (required) -> Create

Test the automatic build trigger by changing e.g. the Readme file in your Github repository. This should trigger a new Docker image build (this may take a while). Check the results on the Docker Hub Build Details tab of the newly created docker repository. After some time, it should show a build status of "done".

Test the image by issuing (change <yourdockername> to your Docker hub username

    ID=$(docker run -d -p 80:80 <yourdockername>/docker-nginx-busybox); echo $ID
    
Check the container status and the logs with

    docker ps
    docker logs $ID
    
And connect to the server by issuing the command

    curl localhost
    
on the docker host. The answer should look like

    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx on busybox!</title>
    ...
    <p><em>Thank you for using nginx.</em></p>
    </body>
    </html>
    
DONE.
