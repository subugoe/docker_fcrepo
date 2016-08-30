FROM tomcat:8.0-jre8
#FROM tomcat:9.0-jre8
#FROM jetty:9.2

MAINTAINER  Jörg Panzer, SUB


ENV FEDORA_VERSION 4.5.1
ENV FEDORA_TAG 4.5.1

RUN echo $FEDORA_VERSION


ENV USER_UID=10021
ENV USER_GID=999

RUN groupadd -r -g $USER_GID fcrepo && \
  useradd -r -u $USER_UID -g $USER_GID fcrepo



ENV BASE_DIR /var/lib/tomcat



ENV KARAF_VERSION 4.0.5
ENV KARAF_HOME  /opt/karaf

ENV MAVEN_OPTS  "-Xmx1024m -XX:MaxMetaspaceSize=1024m"
ENV MAVEN_VERSION 3.3.9
ENV MAVEN_HOME /usr/share/maven
ENV PATH "$PATH:$MAVEN_HOME/bin"

# Install Tomcat 7.
ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH


ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.home=$BASE_DIR/fcrepo4-data"
ENV JAVA_OPTS "${JAVA_OPTS} -Djava.io.tmpdir=/tmp"

#fcrepo.modeshape.configuration=<classpath:/config/repository.json | file:/path/to/repository.json>   $CATALINA_HOME/webapps/fcrepo/WEB-INF/classes/config/minimal-default/repository.json
ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.modeshape.configuration=classpath:/config/minimal-default/repository.json"

#fcrepo.ispn.configuration=<config/infinispan.xml>    /usr/local/tomcat/webapps/fcrepo/WEB-INF/classes/config/infinispan/file/infinispan.xml
ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.ispn.configuration=config/infinispan/file/infinispan.xml"


# the following variables use the fcrepo.home as basepath if not set explicit
ENV JAVA_OPTS "${JAVA_OPTS} -Dcom.arjuna.ats.arjuna.common.ObjectStoreEnvironmentBean.default.objectStoreDir=$BASE_DIR/fcrepo4-data/com.arjuna.ats.arjuna.common.ObjectStoreEnvironmentBean.default.objectStoreDir"
ENV JAVA_OPTS "${JAVA_OPTS} -Dcom.arjuna.ats.arjuna.objectstore.objectStoreDir=$BASE_DIR/fcrepo4-data/com.arjuna.ats.arjuna.objectstore.objectStoreDir"
ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.ispn.CacheDirPath=$BASE_DIR/fcrepo4-data/fcrepo.ispn.cache"
ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.ispn.binary.CacheDirPath=$BASE_DIR/fcrepo4-data/fcrepo.ispn.binary.cache"
ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.modeshape.index.location=$BASE_DIR/fcrepo4-data/index"
ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.ispn.alternative.CacheDirPath=$BASE_DIR/fcrepo4-data/fcrepo.ispn.alternative.cache"
ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.ispn.binary.alternative.CacheDirPath=$BASE_DIR/fcrepo4-data/fcrepo.ispn.binary.alternative.cache"


#ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.ispn.repo.cache=$BASE_DIR/fcrepo4-data/fcrepo.ispn.repo.cache"
#ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.activemq.directory=$BASE_DIR/fcrepo4-data/fcrepo.activemq.directory"


ENV JAVA_OPTS "${JAVA_OPTS} -Dfcrepo.log=DEBUG"
#ENV JAVA_OPTS "${JAVA_OPTS} -server -Xms512M -Xmx1792M -XX:MaxPermSize=256M"
ENV JAVA_OPTS "${JAVA_OPTS} -server -Xms512M -Xmx1792M"

# ---------------------
#

RUN apt-get update && \
    apt-get -y install  git  curl  vim



#----------------
# Install Fedora4



WORKDIR /tmp
RUN curl -fSL   https://github.com/fcrepo4/fcrepo4/releases/download/fcrepo-$FEDORA_TAG/fcrepo-webapp-$FEDORA_VERSION.war -o fcrepo.war
              # https://github.com/fcrepo4/fcrepo4/releases/download/fcrepo-4.5.1/fcrepo-webapp-4.5.1.war



#------------------
# Install on tomcat

RUN sed -i '$i<Resources cachingAllowed="true" cacheMaxSize="100000" />'  /usr/local/tomcat/conf/context.xml
COPY scripts/tomcat-users.xml /usr/local/tomcat/conf/tomcat-users.xml

RUN mkdir -p /var/lib/tomcat/fcrepo4-data
RUN chown -R fcrepo:$USER_GID /var/lib/tomcat/fcrepo4-data


RUN cp fcrepo.war /usr/local/tomcat/webapps/fcrepo.war
RUN chown fcrepo:$USER_GID /usr/local/tomcat/webapps/fcrepo.war


WORKDIR /usr/local/tomcat
RUN chown -R fcrepo:$USER_GID bin webapps temp logs work conf \
   && chmod -R 755 bin webapps temp logs work conf


#RUN mkdir -p /usr/local/tomcat/conf/Catalina/localhost
#RUN chmod -R 755 /usr/local/tomcat/
#RUN chown -R fcrepo:$USER_GID /usr/local/tomcat/




#--------------
# install maven


RUN curl -fsSL http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar xzf - -C /usr/share
RUN mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven
RUN ln -s /usr/share/maven/bin/mvn /usr/bin/mvn


#---------------------
# Install Apache Karaf   (see: https://hub.docker.com/r/yinlinchen/fcrepo4-docker/)


RUN cd /tmp \
 && curl -fSL http://artfiles.org/apache.org/karaf/$KARAF_VERSION/apache-karaf-$KARAF_VERSION.tar.gz -o apache-karaf-$KARAF_VERSION.tar.gz \
 && tar -xzf apache-karaf-$KARAF_VERSION.tar.gz \
 && ls -l /tmp \
 && mv /tmp/apache-karaf-$KARAF_VERSION /opt \
 && ln -s /opt/apache-karaf-$KARAF_VERSION /opt/karaf \
 && rm apache-karaf-$KARAF_VERSION.tar.gz*


RUN chown -R fcrepo:$USER_GID /opt/karaf
RUN chown -R fcrepo:$USER_GID /opt/apache-karaf-$KARAF_VERSION
#    && chmod -R 750 /opt/karaf/etc \
#    && chmod -R 750 /opt/apache-karaf-$KARAF_VERSION/etc


#---------------------
# Fedora Camel Toolbox
COPY scripts/fedora_camel_toolbox.script /fcrepo_config/
COPY scripts/fedora_camel_toolbox.sh /fcrepo_config/
COPY scripts/runall.sh /fcrepo_config/






USER fcrepo



#-------------
# start tomcat

WORKDIR /usr/local/tomcat
#CMD [ "sh", "/usr/local/tomcat/bin/catalina.sh", "run"]



# run with camel modules (solr, fuseki, ...)
CMD [ "sh", "/fcrepo_config/runall.sh"]