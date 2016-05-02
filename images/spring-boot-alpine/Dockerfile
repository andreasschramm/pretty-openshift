
FROM jeanblanchard/java:8

MAINTAINER andreas.schramm@axa.de

ENV WORK_DIRECTORY /opt/openshift

RUN mkdir -p $WORK_DIRECTORY && chmod -R 777 $WORK_DIRECTORY

WORKDIR $WORK_DIRECTORY
ENV MAX_HEAP 768m
ENV MIN_HEAP 256m
ENV JAVA_OPTS -server -XX:MaxPermSize=192m -Djava.net.preferIPv4Stack=true
CMD JAR=echo *.jar && java \
  -Xms${MIN_HEAP} -Xmx${MAX_HEAP} \
  -cp '${WORK_DIRECTORY}' \
  ${JAVA_OPTS} \
  -jar ${JAR[0]}