# wildflyandjdk
Wildfly (>= 25) on adopt openjdk (>=8)

This repository is based on [this](https://github.com/svolotin/wildfly14adoptopenjdk8) and [its fork](https://github.com/posapiano-ops/wildfly14openjdk8).

The *image* is built in the following way:

- **First layer**: the image uses [AdoptOpenJDK 8 (8u172)](https://github.com/AdoptOpenJDK/openjdk-docker/blob/master/8/jdk/ubuntu/Dockerfile.hotspot.releases.full) installed on ubuntu. **Java EE8, Java 8**.

- **Second layer**: the image installs [Wildfly](https://github.com/wildfly/wildfly/releases/download/$WILDFLY_VERSION/wildfly-$WILDFLY_VERSION.tar.gz) (version 25.0.1 Final by default), necessary drivers ([postgres driver](http://www.java2s.com/ref/jar/download-postgresql4222jar-file.html)), [Eclipselink 2.7.2](https://mvnrepository.com/artifact/org.eclipse.persistence/org.eclipse.persistence.jpa/2.7.2), [pdftk](https://en.wikipedia.org/wiki/PDFtk), [qrencode](https://fukuchi.org/works/qrencode/manual/index.html), [dmtx-utils](https://github.com/dmtx/dmtx-utils), [imagemagick](https://imagemagick.org/index.php) and sets jboss as an user instead of root.

### **How to use it**

Recommended way to use this image is to build your application in to next layer using own docker image like this:
```docker
FROM .../wildflyandjdk

ADD ./deployments /opt/jboss/wildfly-{WILDFLY-VERSION}/standalone/deployments #(for deploying your war)

CMD ["/opt/jboss/wildfly-{WILDFLY-VERSION}/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0" , "--debug"]
```
where `{WILDFLY-VERSION}` is 25.0.1.Final by default. 
You can use a custom standalone.xml just adding  "-c", "standalone.xml".
