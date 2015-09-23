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
            inet 10.1.42.1  netmask 255.255.0.0  broadcast 0.0.0.0
            inet6 fe80::3c0c:99ff:feb1:7977  prefixlen 64  scopeid 0x20<link>
            ether 92:2f:30:74:1b:34  txqueuelen 0  (Ethernet)
            RX packets 1098  bytes 53481 (52.2 KiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 1725  bytes 1820656 (1.7 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    
    eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
            inet6 fe80::a00:27ff:fe30:8234  prefixlen 64  scopeid 0x20<link>
            ether 08:00:27:30:82:34  txqueuelen 1000  (Ethernet)
            RX packets 176628  bytes 208651633 (198.9 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 90945  bytes 5658801 (5.3 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    
    eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 172.17.8.101  netmask 255.255.255.0  broadcast 172.17.8.255
            inet6 fe80::a00:27ff:fee2:7464  prefixlen 64  scopeid 0x20<link>
            ether 08:00:27:e2:74:64  txqueuelen 1000  (Ethernet)
            RX packets 20653  bytes 2043827 (1.9 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 297  bytes 21895 (21.3 KiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    
    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
            inet 127.0.0.1  netmask 255.0.0.0
            inet6 ::1  prefixlen 128  scopeid 0x10<host>
            loop  txqueuelen 0  (Local Loopback)
            RX packets 897095  bytes 186016501 (177.3 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 897095  bytes 186016501 (177.3 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    
    vethe5cab35: flags=67<UP,BROADCAST,RUNNING>  mtu 1500
            inet6 fe80::902f:30ff:fe74:1b34  prefixlen 64  scopeid 0x20<link>
            ether 92:2f:30:74:1b:34  txqueuelen 0  (Ethernet)
            RX packets 24  bytes 2809 (2.7 KiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 35  bytes 2900 (2.8 KiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

All IP addresses are accessible from the docker host, but only eth1 IP address (172.17.8.101 in this case) is accessible from the Windows host, where the Virtualbox docker VM is running. Therefore, in this case

    http://172.17.8.101

will display the NginX welcome page. On the Windows machine.

And even this address is not reachable from an other machine than the Windows host because of routing challenges, if the corresponding IP address is not known to the rest of the network.
