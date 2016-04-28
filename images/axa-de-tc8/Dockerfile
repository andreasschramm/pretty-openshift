FROM jboss-webserver-3/webserver30-tomcat8-openshift:latest

MAINTAINER andreas.schramm@axa.de

USER root

COPY files/java_home__jre__lib__security/* ${JAVA_HOME}/jre/lib/security/

COPY files/etc__pki__ca-trust__source__anchors/* /etc/pki/ca-trust/source/anchors/

RUN update-ca-trust && update-ca-trust enable

USER jboss
