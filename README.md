# (Kubernetes) DevTools

This is a quick way to spin up a tooling platform on an existing Kubernetes cluster (or Minikube). It's ideal for proof of concepts and training purposes covering the DevOps & Kubernetes areas. Each tool is contained within the ```tools/``` folder in the form of a helm chart. For more information about helm please see here - https://helm.sh/docs/intro/install

## Why (Kubernetes) DevTools?
The Docker images used are taken straight Docker Hub (where possible) hence are maintained by the vendors themselves. Additionally all configuration to the tooling is externalised in the form of ConfigMaps. This makes it easy to test, upgrade & maintain the platform in the long-term.

## Prerequisites

Please make sure the following prerequisites are met:

- An existing Kubernetes Cluster (v1.17.0 or higher) - This can also be Minikube (See below for setting up Minikube) 
- The ```kubectl``` client (v1.17.0 or higher) - Available from here - https://kubernetes.io/docs/tasks/tools/install-kubectl/
- Helm v3 - Available from here - https://helm.sh/docs/intro/install/


## Included Tools
The following tools are included

|Tool|Version|Documentation Link|
|---------------------------|-----------------------------------|-----------------------------------|
|Jenkins|2.222.3|https://www.jenkins.io|
|Gerrit|3.1.4|https://www.gerritcodereview.com|
|LDAP|2.4.44|https://www.openldap.org|
|LDAP Admin Console|0.7.1|http://www.ldapadmin.org|
|Nexus|3.22.1|https://www.sonatype.com/product-nexus-repository|
|Sonarqube|6.7|https://www.sonarqube.org|
|Nginx|1.17.7|https://www.nginx.com|

## Setting up Minikube

**NOTE:** Please skip this step if you already have a kubernetes cluster.

Start Minikube by running (4 vCPUs and 8GB of memory is recommended for running the full platform on Minikube):

```sh
# Stop & delete your old minikube instance
minikube stop
minikube delete

# Start the Minikube Cluster with the ingress addon
# Use a VM driver as the Docker driver does not support ingress 
minikube start --cpus 4 --memory 8196 --addons=ingress --vm=true
```

To allow your browser to resolve the Minikube address as a DNS, update your hosts file to include the entry for the Minikube ingress once it has been created
```sh
# Mac OS / Linux - Hosts File
/etc/hosts
# Windows Hosts File
C:\Windows\System32\drivers\etc\hosts
```


## Launching The Platform
Ensure that a Kubernetes cluster of the current version is setup using Minikube as above or exists already. The following steps can be taken to deploy the toolset to the cluster.

### Configure the root ```values.yaml```
Modify the ```global.tools.enable``` keys in ```tools/values.yaml``` to determine which components of the (Kubernetes) DevTools to install.
```yaml
################ COMPONENT CONTROLS ################
global:
  tools:
    enable: # Which tools to enable as part of the platform
      ldap: true
      sonarqube: false
      jenkinsMaster: false
      nginx: true
      nexus: false
      gerrit: false
      extras: false
  jenkinsNamespace: # Set to true, if the jenkins slave needs to be in its own namespace.
    create: false
```

**NOTE**: If an additional component is required after installation, simply set it to ```true```, and rerun the helm steps detailed below.
**NOTE**: If an additional configuration is required for the components, please navigate to the individual ```values.yaml``` files inside the relevant charts and update those values.

### Install Using Helm

Run the following helm commands to install (Kubernetes) DevTools on your cluster.

Add the MySQL Chart and run the Helm Install/Upgrade to install the platform.
```sh
cd tools/
# Needed for Gerrit & Sonarqube MySQL
helm repo add mysql https://kubernetes-charts.storage.googleapis.com/
helm repo update
helm dependency build
helm dependency update
helm upgrade devtools --install . [parameters]
```
![Helm Operations](img/helm_operations.gif)

Parameters can be passed in to the helm command in the following format. The table below shows the available parameters.
```sh
helm upgrade devtools --install tools/ --set PARAMETER1=VALUE1,PARAMETER2=VALUE2
```

## Access The Toolset
Once all the services have finished deployed, the platform can be accessed on: ```http://<INGRESS_IP>```

The Ingress IP can be obtained by running the following. Example below:
```sh
$ kubectl get ingress nginx -o wide | awk '{ print $4}'
ADDRESS
192.168.65.14
```
It is possible to then login to the cluster by navigating to ```http://<INGRESS_IP>```.
It is then possible to login with:
* Non-admin Credentials:
    * DN: yourusername,ou=people,dc=ldap,dc=example,dc=org
    * Password: yourpassword
* Admin Credentials:
    * DN: cn=admin,dc=ldap,dc=example,dc=org
    * Password: generated in secret

The password for a secret can be obtained by doing the following:
```sh
# Obtain the Administrative password
kubectl get secret ldap-secret -o jsonpath="{.data.ldap-password}" | base64 --decode

# Obtain a non-admin user password. E.g. Jenkins
kubectl get secret ldap-secret -o jsonpath="{.data.jenkins-password}" | base64 --decode
```

## The Incubator
The incubator exists in the folder ```incubator```. Items in the incubator are in development and will not be installed as part of the (Kubernetes) DevTools chart. But they might be part of a future release.

# License
Please view the ```LICENSE.md``` file at the root of this repository for licensing information.

# Disclaimer
This project will not create a Kubernetes cluster for you on any cloud provider. It assumes that you have a Kubernetes cluster and the appropiate permissions to deploy this onto the cluster. In order to create a new namespace Helm will need permissions to do so. If using RBAC on your cluster, make sure you assign permissions accordingly. In order to separate this out, you can run the Namespace and ServiceAccount yaml (in that order) separately from your charts.

This project is self-contained, if integration with cloud native services is required, please modify the helm templates as you see fit. E.g. Replacing standard storage class with EBS when running on AWS.

When deleting your (Kubernetes) DevTools release there are a few artifacts that linger if the Jenkins slave is enabled:
- Jenkins ServiceAccount
- Jenkins slaves namespace and any resources in it
Please make sure to clean these up as needed.

## Docker Image Disclaimer
The USP of (Kubernetes) DevTools is the fact that all Docker images are open-source and maintained by vendors & 3rd parties - Making maintenance of the platform simpler. We do not necessarily maintain the images referenced in the helm charts. The (Kubernetes) DevTools project will only consume updates directly from the vendors/3rd parties.

# User Feedback
## Documentation
This tooling platform consists of a number of open-source tools. The documentation for each tool can be found can be on the individual tool websites. The documentation for each tool running on Kubernetes is detailed in the sub-level ```README.md```

## Issues
If you have any problems with or questions about this image, please contact us through a GitHub issue.

## Contribute
You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

Before you start to code, we recommend discussing your plans through a GitHub issue, especially for more ambitious contributions. This gives other contributors a chance to point you in the right direction, give you feedback on your design, and help you find out if someone else is working on the same thing.
