FROM registry.access.redhat.com/jboss-webserver-5/webserver50-tomcat9-openshift:1.2-13
USER 0
RUN yum install patch -y

USER 185
COPY contrib/server.xml /opt/jws-5.0/tomcat/conf/
RUN curl -sSL https://github.com/jfclere/tomcat-kubernetes/raw/master/sample.war -o $JWS_HOME/webapps/ROOT.war
