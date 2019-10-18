# ha-jboss-tomcat
HA JBoss with Tomcat

based on https://github.com/jfclere/tomcat-kubernetes


oc run jboss --serviceaccount=privileged --image=registry.access.redhat.com/jboss-webserver-5/webserver50-tomcat9-openshift:1.2-13 -it --rm --command --restart=Never --overrides='{"spec": { "securityContext": {"runAsUser": "0"}}}' -- sh

oc import-image tomcat:9.0.27-jdk8-openjdk --from=tomcat:9.0.27-jdk8-openjdk --reference-policy=local --confirm --dry-run -o yaml

oc new-build . --name=ha-webserver50-tomcat9 --dry-run -o yaml > openshift/build.yaml
oc process -f openshift/build.yaml | oc create -f -
oc process -f openshift/build.yaml | oc replace -f -

oc new-app --image-stream=ha-webserver50-tomcat9:latest --name=ha-webserver50-tomcat9 --dry-run -o yaml > openshift/deployment.yaml

oc process -f openshift/deployment.yaml | oc replace -f -
