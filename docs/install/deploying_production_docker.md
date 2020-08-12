e-mission has a fairly standard docker installation. 
However, I've found myself setting up servers from scratch a couple of times recently, and thought that it would be useful to write down the steps in case it is useful to have them listed in a single location.

1. [Install docker on ubuntu](https://docs.docker.com/engine/install/ubuntu/)

```
sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete
Digest: sha256:49a1c8800c94df04e9658809b006fd8a686cab8028d33cfba2cc049724254202
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

1. [Install docker-compose on ubuntu](https://docs.docker.com/compose/install/)

```
$ docker-compose --version
docker-compose version 1.26.2, build eefe0d31
```

1. Check out the [e-mission-docker repo](https://github.com/e-mission/e-mission-docker)

```
ls -a1 e-mission-docker/
.
..
bin
clone_and_start_server.sh
Dockerfile
Dockerfile.dev.server-only
Dockerfile.dev.ui-only
Dockerfile.nomkl
examples
.git
LICENSE
README.md
start_devapp_serve.sh
start_script.sh
```

1. Copy out the multi-tier sample to a directory for this particular instance

```
$ cp -r e-mission-docker/examples/em-server-multi-tier-cronjob emdemo
```

1. Change all the `CHANGEME` fields in the example + any other config changes needed

```
~/emdemo$ grep -r CHANGEME .
./README.md:- you should look through all the `CHANGEME` locations and fill them out
./README.md:$ grep -r CHANGEME .
~/emdemo$
```

1. Launch the server!

```
~/emdemo$ sudo docker-compose up
...
Creating network "emdemo_emission" with the default driver
Creating volume "emdemo_mongo-data" with default driver
...
Building analysis-server
...
Successfully tagged emdemo_analysis-server:latest
Building web-server
...
Successfully tagged emdemo_web-server:latest
...
web-server_1       | }
db_1               | 2020-08-12T05:27:21.126+0000 I FTDC     [initandlisten] Initializing full-time diagnostic data capture with directory '/data/db/diagnostic.data'
...
```

1. Test that it works ✔️

```
$ curl http://localhost:<exposed port in docker-compose>
<html>
...
</html>
```
