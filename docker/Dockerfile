FROM tomcat:9.0.27-jdk8-openjdk
#FROM registry.access.redhat.com/jboss-webserver-5/webserver50-tomcat9-openshift:1.2-13
COPY contrib/patches.patch /usr/local/tomcat/conf/
COPY contrib/original-files.sha1 /usr/local/tomcat/conf/
RUN set -xe && \
    apt-get update -qq && \
    apt-get install -qq -y --no-upgrade patch && \
    rm -rf /usr/local/tomcat/webapps/* && \
    mkdir -p /usr/local/tomcat/conf/Catalina/localhost && \
    chmod g+w /usr/local/tomcat/conf/Catalina/localhost && \
    chmod g+w /usr/local/tomcat/webapps && \
    sha1sum --check /usr/local/tomcat/conf/original-files.sha1 --status && \
    patch --batch --forward --quiet --unified --directory=/usr/local/tomcat/conf --input=/usr/local/tomcat/conf/patches.patch && \
    curl -sSL https://github.com/jfclere/tomcat-kubernetes/raw/master/sample.war -o /usr/local/tomcat/webapps/ROOT.war && \
    curl -sSL https://oss.sonatype.org/content/repositories/public/io/hawt/hawtio-default/2.7.1/hawtio-default-2.7.1.war -o /usr/local/tomcat/webapps/hawtio.war && \
    find /usr/local/tomcat/webapps/ -maxdepth 1 -name '*.war' | xargs basename -s .war | xargs -t -I {} mkdir '/usr/local/tomcat/webapps/{}' && \
    find /usr/local/tomcat/webapps/ -maxdepth 1 -name '*.war' | xargs basename -s .war | xargs -t -I {} unzip -n -qq '/usr/local/tomcat/webapps/{}.war' -d '/usr/local/tomcat/webapps/{}' && \
    find /usr/local/tomcat/webapps/ -maxdepth 1 -name '*.war' | xargs -t -I {} rm '{}' && \
    apt-get remove -qq -y --purge patch && apt-get clean && set +x && rm -rf /var/lib/apt/lists/* && rm -rf /tmp/*
RUN echo mv /usr/local/tomcat/conf/logging.properties  /usr/local/tomcat/conf/logging.properties.bck
COPY contrib/logging.properties /usr/local/tomcat/conf/
RUN echo mv /usr/local/tomcat/conf/server.xml  /usr/local/tomcat/conf/server.xml.bck
COPY contrib/server.xml /usr/local/tomcat/conf/
ENV JPDA_ADDRESS="8090"
ENV JPDA_TRANSPORT="dt_socket"
ENV JPDA_SUSPEND="n"
ENTRYPOINT ["catalina.sh", "jpda", "run"]



