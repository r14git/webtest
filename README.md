docker-nginx-busybox
====================

This is a teeny tiny docker image for nginx built on top of busybox.

Manually build a docker image
-----------------------------
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
On boot2docker hosts (Windows and iOS), find your docker host's IP address by issuing the command 
    
    boot2docker ip
     
on the boot2docker start commandline window.

If you start a browser on the Linux docker host, use the URL 

    http://localhost

If the Linux docker host is hosted on Windows using Virtualbox, using a Browser on Windows or iOS might be more tricky; this holds especially, if the docker host is a Virtualbox VM using a NAT interface only. If you do not want to cope with static NAT entries in Virtualbox, I recommend to provision the Linux docker image with a bridged interface that can be accessed from the outside. If you want to connect to the docker container only from the Windows or iOS machine, a separate host-only network will do as well.

Here an example of a CoreOS machine installed on Windows via Vagrant with a host-only network eth1:

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

All IP addresses are accessible from the docker host, but only eth1 IP address (172.17.8.101 in this case) is accessible from the Windows host, where the Virtualbox docker VM is running. Therefore, in this case

    http://172.17.8.101

will display the NginX welcome page. On the Windows machine.

And even this address is not reachable from an other machine than the Windows host because of routing challenges, if the corresponding IP address is not known to the rest of the network.
