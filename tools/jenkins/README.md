# Jenkins

This chart deploys the Nexus Repository Manager on a pre-existing Kubernetes cluster.

- [`2.222.3` (*2.222.3/Dockerfile*)](https://hub.docker.com/layers/jenkins/jenkins/2.222.3/images/sha256-15fca69266f4cc2107449d9fa2595acf0cbe1584eb425ee6f2fe9cefc9fc4b97?context=explore)

## Prerequisites Details

- Kubernetes 1.6+

## Chart Details

|File|Type|Description|
|---------------------------|-----------------------------------|----------------------------|
|```init/init.yaml```| ```ConfigMap``` | Contains scripts to generate jenkins SSH public key and a ```plugins.txt``` file for the required Jenkins plugins |
|```init/init-groovy.yaml```| ```ConfigMap``` | Contains groovy scripts for setting up Jenkins with Sonarqube |
|```init/jenkins-casc.yaml```| ```ConfigMap``` | Contains Jenkins configuration for it to talk to LDAP|
|```init/load-platform.yaml```| ```ConfigMap``` |  |
|```init/sonar-token.yaml```| ```ConfigMap``` | Contains automation to setup a Sonarqube token with Jenkins |
|```deployment.yaml```| ```Deployment``` | Contains the deployment template for Jenkins  |
|```namespace.yaml```| ```Namepspace``` | Contains the namespace definition for Jenkins slaves (if Jenkins slaves are enabled) |
|```persistentVolume.yaml```| ```PersistentVolume``` | Contains the Persistent Volume definition for Jenkins |
|```service.yaml```| ```Service``` | Contains the Service Defintion for Jenkins |
|```service-account.yaml```| ```ServiceAccount``` | Contains the service account definition (if Jenkins slaves are enabled) |

When this chart is installed, an initContainer is run which is used to setup the Jenkins plugins, basic Jenkins jobs and also integration with Sonar & Nexus (if they are enabled for deployment as part of the overall (Kubernetes) DevTools chart.)

## Installing This Chart
This chart is installed as part of the overall (Kubernetes) DevTools chart but can also be installed individually.

If installing this chart **independently**, please ensure that the following exist in the namespace already:
- A config map called ```ldap-properties``` which contains values detailed in the ```ldap/``` section of this repository
- A secret called ```ldap-secret``` which contains values detailed in the ```ldap/``` section of this repository

Once the above is present, to install with the default settings present in the current level ```values.yaml```:
```sh
helm upgrade jenkins --install .
```

To install and override the persistent value setting to ```false```, use the below: 
```sh
helm upgrade --set persistence.enabled="false" jenkins --install .
```

Or provide your own ```values.yaml``` file:
```sh
helm upgrade -f values.yaml jenkins --install .
```

## Configuration

The following configuration options are available when spinning up this chart. The default LDAP values are configured so that the Nexus service can talk to the LDAP chart that is part of the (Kubernetes) DevTools.

|Parameter|Description|Default|
|---------------------------|-----------------------------------|----------------------------------------------------------|
|```Master.Name```|The name of the jenkins master container|```jenkins-master```|
|```Master.Image```|The jenkins image to use from Docker Hub for the Jenkins master|```jenkins/jenkins```|
|```Master.ImageTag```|The version of the Jenkins docker image to pull|```2.222.3```|
|```Master.ImagePullPolicy```| The pull policy for the image|```Always```|
|```Master.JavaOpts```|The ```JAVA_OPTS``` to pass to the runtime java process |```-Djenkins.install.runSetupWizard=false```|
|```Master.JenkinsOpts```|The ```JENKINS_OPTS``` to pass to the Jenkins Master on startup |``` ```|
|```Master.JenkinsUriPrefix```| The prefix to host Jenkins on |```/jenkins```|
|```Master.resources.requests```| The ```CPU``` & ```Memory``` requests for Jenkins |```CPU:150m, Memory: 256Mi```|
|```Master.resources.limits```| The ```CPU``` & ```Memory``` limits for the Jenkins Master |```CPU:2000m, Memory: 2048Mi```|
|```Master.LDAP.LDAP_ENABLED```| Whether to enable LDAP for Jenkins Auth | ```true```|
|```Master.persistence.storageClass```|The storage class for the persistent storage|```-``` (Makes use of host storage)|
|```Master.persistence.enabled```|The storage class for the persistent storage|```-``` (Makes use of host storage)|
|```Master.InstallPlugins```|The plugins to install for Jenkins (Can be installed through the UI later as well) - Please see |Please see ```values.yaml``` for full list|
|```Slave.enabled```|Whether the Jenkins slave will be setup as well **(Currently in Development)**|```false```|
|```containerPort```|The port that the Jenkins master will listen on |```8080```|
|```replicas```|The number of replicas to have for the master |```1```|