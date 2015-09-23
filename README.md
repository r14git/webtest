docker-nginx-busybox
====================

This is a teeny tiny docker image for nginx built on top of busybox.

Manually build a docker image
-----------------------------
On the docker host, perform the command:
docker build --tag=nginx-busybox:latest .

Start Container from the above created image
--------------------------------------------
On the docker host, perform the command:
docker run -d -p 80:80 nginx-busybox

Connect to the Server
---------------------
...


