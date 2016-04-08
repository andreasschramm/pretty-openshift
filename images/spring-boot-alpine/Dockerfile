
FROM jeanblanchard/java:8

MAINTAINER andreas.schramm@axa.de

ENV WORK_DIRECTORY /work

RUN mkdir -p $WORK_DIRECTORY && chmod -R 777 $WORK_DIRECTORY

WORKDIR $WORK_DIRECTORY
ENV MAX_HEAP 768m
ENV MIN_HEAP 256m
ENV JAVA_OPTS -server -XX:MaxPermSize=192m -Djava.net.preferIPv4Stack=true
CMD java \
  -Xms${MIN_HEAP} -Xmx${MAX_HEAP} \
  -cp '${WORK_DIRECTORY}' \
  ${JAVA_OPTS} \
  -jar `ls -1 *.jar | head -1`