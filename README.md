# wildflyandjdk
Wildfly (default 24) on adopt openjdk (default 8)

This repository is based on [this](https://github.com/svolotin/wildfly14adoptopenjdk8) and [its fork](https://github.com/posapiano-ops/wildfly14openjdk8) (and [this](https://github.com/posapiano-ops/wildfly14vpn) for the vpn).

The basic *image* is built in the following way:

- **First layer**: the image uses [AdoptOpenJDK 8 (8u172)](https://github.com/AdoptOpenJDK/openjdk-docker/blob/master/8/jdk/ubuntu/Dockerfile.hotspot.releases.full) installed on ubuntu. **Java EE8, Java 8**.

- **Second layer**: 
  - **light**: the image installs [Wildfly](https://github.com/wildfly/wildfly/releases/download/$WILDFLY_VERSION/wildfly-$WILDFLY_VERSION.tar.gz) (version 25.0.1 Final by default), necessary drivers ([postgres driver](http://www.java2s.com/ref/jar/download-postgresql4222jar-file.html)), [Eclipselink 2.7.2](https://mvnrepository.com/artifact/org.eclipse.persistence/org.eclipse.persistence.jpa/2.7.2) and sets jboss as an user instead of root.
  - **latest**: it adds some tools to **light** version like [pdftk](https://en.wikipedia.org/wiki/PDFtk), [qrencode](https://fukuchi.org/works/qrencode/manual/index.html), [dmtx-utils](https://github.com/dmtx/dmtx-utils), [imagemagick](https://imagemagick.org/index.php) and sets jboss as an user instead of root.
  - **vpn**: the images contains [openconnct](http://www.infradead.org/openconnect/), [net-tools](https://net-tools.sourceforge.io/), [iputils-ping](https://github.com/iputils/iputils/blob/master/ping/ping.c), [iptables](https://en.wikipedia.org/wiki/Iptables), [tcpdump](https://www.tcpdump.org/) and [telnet](https://en.wikipedia.org/wiki/Telnet)

## **How to use it**

Recommended way to use this image (where `{WILDFLY-VERSION}` is 24.0.1.Final by default):
### Latest or light
* Docker-compose:
```docker
wildflyandjdk:
  container_name: wildflyvpn
  image: nigia/wildflyandjdk:24.light #or 24.latest
  ports:
    - 9990:8080
    - 8080:9990
  networks:
    - mynetwork
```
* Dockerfile:
```docker
FROM nigia/wildflyandjdk:24.light #or 24.latest

ADD ./deployments /opt/jboss/wildfly-{WILDFLY-VERSION}/standalone/deployments #(for deploying your war)

CMD ["/opt/jboss/wildfly-{WILDFLY-VERSION}/bin/standalone.sh", "-c", "standalone.xml", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0" , "--debug"]
```
### Vpn
* Status Vpn is:
   * `0` connected
   * `1` disconnected

* Run in bash shell
```bash
# Start 
/opt/vpn/startVpn.sh
# Stop
/opt/vpn/stopVpn.sh
# Status
/opt/vpn/vpnStatus.sh
```

* Docker-compose:
```docker
wildfly-vpn:
  container_name: wildflyvpn
  image: posapiano/wildflyandjdk:24.vpn
  privileged: true
  env_file:
    - VPN_URL: "https://vpn_link"
    - VPN_USER: "utente"
    - VPN_PASSWORD: "passowrd"
    - END_POINT_CHECK: "https://endpoint"
  ports:
    - 9990:8080
    - 8080:9990
  networks:
    - mynetwork
```
* Dockerfile:
```docker
FROM dryseawind/wildfly14jdk8

ADD ./deployments /opt/jboss/wildfly-14.0.1.Final/standalone/deployments (for deploying your war)

ENV VPN_URL="https://vpn_link"
ENV VPN_USER="utente"
ENV VPN_PASSWORD="passowrd"
ENV END_POINT_CHECK="https://endpoint"

CMD ["/opt/jboss/wildfly-{WILDFLY-VERSION}/bin/standalone.sh", "-c", "standalone.xml", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0" , "--debug"]
```

DockerHub: https://hub.docker.com/r/nigia/wildflyandjdk
