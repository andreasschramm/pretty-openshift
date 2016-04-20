FROM jeanblanchard/java:8

#Download neuxs
ENV NEXUS_VERSION 3.0.0-b2015110601

# Install the binaries
RUN mkdir -p /opt/nexus/nexus \
    &&  apk add --update curl && \
    && curl --fail --silent --location --retry 3 \
    https://sonatype-download.global.ssl.fastly.net/nexus/oss/nexus-installer-${NEXUS_VERSION}-unix-archive.tar.gz
    | gunzip \
    | tar x -C /tmp nexus-${NEXUS_VERSION} \
    && mv /tmp/nexus-${NEXUS_VERSION}/* /opt/nexus/nexus/ \
    && rm -rf /tmp/nexus-${NEXUS_VERSION}


#configure nexus
ENV NEXUS_HOME /opt/nexus/nexus
ENV NEXUS_WORK /sonatype-work
ENV NEXUS_REPOS /repositories

RUN  chmod 777 $NEXUS_HOME \
   && mkdir -p $NEXUS_WORK && chmod -R 777 $NEXUS_WORK \
   && mkdir -p $NEXUS_REPOS && chmod -R 777 $NEXUS_REPOS

RUN groupadd -r jboss -g 185 && useradd -u 185 -r -g jboss -m -d /home/jboss -s /sbin/nologin -c "JBoss user" jboss


COPY scripts/* /usr/local/bin/

COPY nexus_work $NEXUS_WORK/

COPY nexus_home $NEXUS_HOME/

COPY secret /secret

RUN chmod 755 /usr/local/bin/fixPermissions.sh
RUN chmod 755 /usr/local/bin/readinessProbe.sh

RUN  /usr/local/bin/fixPermissions.sh /opt/nexus
RUN  /usr/local/bin/fixPermissions.sh $NEXUS_WORK
RUN  /usr/local/bin/fixPermissions.sh $NEXUS_REPOS
RUN  /usr/local/bin/fixPermissions.sh /secret

VOLUME ["/sonatype-work", "/repositories", "/secret"]

USER jboss
WORKDIR $NEXUS_HOME

EXPOSE 8081

ENV CONTEXT_PATH /
ENV MAX_HEAP 768m
ENV MIN_HEAP 256m
ENV JAVA_OPTS -server -XX:MaxPermSize=192m -Djava.net.preferIPv4Stack=true
ENV LAUNCHER_CONF ${NEXUS_HOME}/conf/jetty.xml ${NEXUS_HOME}/conf/jetty-requestlog.xml ${NEXUS_HOME}/conf/jetty-https.xml

CMD cat /secret/ssl-keypassword \
	&& SSL_PASSWORD=`cat /secret/ssl-password` \
	&& SSL_KEYPASSWORD=`cat /secret/ssl-keypassword` \
	&& java \
  -Dnexus-work=${NEXUS_WORK} -Dnexus-webapp-context-path=${CONTEXT_PATH} -Dapplication-conf=${NEXUS_HOME}/conf \
  -Dorg.eclipse.jetty.ssl.keypassword=`cat /secret/ssl-keypassword` \
  -Dorg.eclipse.jetty.ssl.password=`cat /secret/ssl-password` \
  -Xms${MIN_HEAP} -Xmx${MAX_HEAP} \
  -cp '/opt/nexus/nexus/conf/:/opt/nexus/nexus/lib/*' \
  ${JAVA_OPTS} \
  org.sonatype.nexus.bootstrap.Launcher ${LAUNCHER_CONF}
