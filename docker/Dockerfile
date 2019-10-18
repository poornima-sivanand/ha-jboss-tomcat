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
    apt-get remove -qq -y --purge patch && apt-get clean && set +x && rm -rf /var/lib/apt/lists/* && rm -rf /tmp/*
