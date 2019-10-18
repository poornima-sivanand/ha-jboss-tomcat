# ha-jboss-tomcat
HA JBoss with Tomcat

based on https://github.com/jfclere/tomcat-kubernetes


oc run jboss --serviceaccount=privileged --image=registry.access.redhat.com/jboss-webserver-5/webserver50-tomcat9-openshift:1.2-13 -it --rm --command --restart=Never --overrides='{"spec": { "securityContext": {"runAsUser": "0"}}}' -- sh

oc import-image tomcat:9.0.27-jdk8-openjdk --from=tomcat:9.0.27-jdk8-openjdk --reference-policy=local --confirm --dry-run -o yaml

oc new-build . --name=ha-tomcat9 --dry-run -o yaml > openshift/build.yaml
oc process -f openshift/build.yaml | oc create -f -
oc process -f openshift/build.yaml | oc replace -f -

oc start-build ha-tomcat9 --from-dir=.

oc new-app --image-stream=ha-webserver50-tomcat9:latest --name=ha-webserver50-tomcat9 --dry-run -o yaml > openshift/deployment.yaml

oc policy add-role-to-user view -z tomcat --rolebinding-name=tomcat

oc process -f openshift/deployment.yaml | oc create -f -

oc cp tomcat:/usr/local/tomcat/conf/server.xml docker/contrib/server.original.xml
cp docker/contrib/server.original.xml docker/contrib/server.xml

diff docker/contrib/server.original.xml docker/contrib/server.xml > docker/contrib/server.xml.patch

cp docker/contrib/server.original.xml docker/contrib/server2.xml
patch --batch --input=docker/contrib/server.xml.patch --output=docker/contrib/server2.xml docker/contrib/server.xml

oc policy add-role-to-user tomcat -z tomcat --dry-run -o yaml
oc create role tomcat --verb=get,list,watch --resource=pods --dry-run -o yaml
