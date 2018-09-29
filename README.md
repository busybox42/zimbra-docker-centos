[![](https://images.microbadger.com/badges/image/busybox42/zimbra-docker-centos.svg)](https://microbadger.com/images/busybox42/zimbra-docker-centos "Get your own image badge on microbadger.com")
[![](https://images.microbadger.com/badges/version/busybox42/zimbra-docker-centos.svg)](https://microbadger.com/images/busybox42/zimbra-docker-centos "Get your own version badge on microbadger.com")

# Zimbra
In this Repository you will find how to install Zimbra on Docker

# Docker
## How to install Docker
Keep posted of the changes in the next Zimbra Wiki - https://wiki.zimbra.com/wiki/Deploy_Zimbra_Collaboration_using_docker

Depend of your OS, you need to install Docker in different ways, take a look into the official Website - https://docs.docker.com/engine/installation/

One of the advantages of using docker is that the host OS does not matter, the containers will work on any platform.

## Downloading the image
The first step is to pull this image into your docker environment, for that just run the next:
```bash
docker pull busybox42/zimbra-docker-centos
```

## Creating Zimbra Containers
Now that we have an image called busybox42/zimbra-docker-centos we can do a docker run with some special parameters, like this:
```bash
docker run -p 25:25 -p 80:80 -p 465:465 -p 587:587 -p 110:110 -p 143:143 -p 993:993 -p 995:995 -p 443:443 -p 8080:8080 -p 8443:8443 -p 7071:7071 -p 9071:9071 -h zimbra-docker.zimbra.io --dns 127.0.0.1 --dns 8.8.8.8 -i -t -e PASSWORD=Zimbra2017 busybox42/zimbra-docker-centos
```
As you can see we tell the container the ports we want to expose, and on which port, we also specify the container hostname, the password foir the Zimbra Administrator Account, and the image to use.

That's it! You can visit now the IP of your Docker Machine using HTTPS, or try the Admin Console with HTTPS and port 7071.

## Contribute to the Project
If you like to contribute to the project, you are free to do so, just fork this repo and submit your changes.

# Manual process - not really recommended

<details>
  <summary>Manual process</summary>

## Creating the Zimbra Image

 The content of the Dockerfile and the start.sh is based on the next Script - ZimbraEasyInstall. The Dockerfile creates a CentOS 7 image and install on it all the OS dependencies which Zimbra needs, when the container is launched, automatically starts with the start.sh script which creates an autoconfig file which is injected during the zimbra Installation.

### Using git
Download from github, you will need git installed on your OS

```bash
git clone https://github.com/busybox42/zimbra-docker-centos.git
```
### Using wget
For those who want to use wget, follow the next instructions to download the Zimbra-docker package. You might need wget and unzip installed on your OS
```bash
wget https://github.com/busybox42/zimbra-docker-centos/archive/master.zip
unzip master.zip
```

### Build the image using the Dockerfile
The `Makefile` in the docker/ directory provides you with a convenient way to build your docker image. You will need make on your OS. Just run

```bash
cd zimbra-docker-centos/docker
sudo make
```

The default image name is zimbra_docker.

### Deploy the Docker container
Now, to deploy the container based on the previous image. As well as publish the Zimbra Collaboration ports, the hostname and the proper DNS, as you want to use bind as a local DNS nameserver within the container, also we will send the password that we want to our Zimbra Server like admin password, mailbox, LDAP, etc.: Syntax:
```bash
docker run -p PORTS -h HOSTNAME.DOMAIN --dns DNSSERVER -i -t -e PASSWORD=YOURPASSWORD NAMEOFDOCKERIMAGE
```
Example:
```bash
docker run -p 25:25 -p 80:80 -p 465:465 -p 587:587 -p 110:110 -p 143:143 -p 993:993 -p 995:995 -p 443:443 -p 8080:8080 -p 8443:8443 -p 7071:7071 -p 9071:9071 -h zimbra-docker.zimbra.io --dns 127.0.0.1 --dns 8.8.8.8 -i -t -e PASSWORD=Zimbra2017 zimbra_docker_centos
```
Depending out your limits settings you may need to add a --ulimits option.
Example:
```bash
docker run -p 25:25 -p 80:80 -p 465:465 -p 587:587 -p 110:110 -p 143:143 -p 993:993 -p 995:995 -p 443:443 -p 8080:8080 -p 8443:8443 -p 7071:7071 -p 9071:9071 -h zimbra-docker.zimbra.io --dns 127.0.0.1 --dns 8.8.8.8 -i -t -e PASSWORD=Zimbra2017 --ulimit nofile=524288:524288 zimbra_docker_centos
```

This will create the container in few seconds, and run automatically the start.sh:

* Install a DNS Server based in dnsmasq
* Configure all the DNS Server to resolve automatically internal the MX and the hostname that we define while launch the container.
* Install a fresh Zimbra Collaboration 8.8.8 within Zimbra Chat and Drive!
* Create 2 files to automate the Zimbra Collaboration installation, the keystrokes and the config.defaults.
* Launch the installation of Zimbra based only in the .install.sh -s
* Inject the config.defaults file with all the parameters that is autoconfigured with the Hostname, domain, IP, and password that you define before.

The script takes a few minutes, dependent on the your Internet Speed, and resources.

</details>

## Known issues

After the Zimbra automated installation, if you close or quit the bash console from the container, the docker container might exit and keep in stopped state, you just need to run the next commands to start your Zimbra Container:

```bash
docker ps -a 
docker start YOURCONTAINERID
docker exec -it YOURCONTAINERID bash
su - zimbra
zmcontrol restart
```
