# Gerrit
This chart deploys Gerrit on a pre-existing Kubernetes cluster.

- [`3.1.4` (*3.1.4/Dockerfile*)](https://hub.docker.com/layers/gerritcodereview/gerrit/3.1.4/images/sha256-a0d8fbddfb7244dc1652755427188d3cf6fab3be02ab8e599d5d0260f39365ea?context=explore)

## Prerequisites Details

- Kubernetes 1.6+

## Chart Details

|File|Type|Description|
|---------------------------|-----------------------------------|----------------------------|
|```init/init.yaml```| ```ConfigMap``` | Contains a shell script which runs Gerrit initialisation with LDAP and also MySQL  |
|```init/config-groups.yaml```| ```ConfigMap``` | Contains scripts which will setup Gerrit roles and map them to LDAP   |
|```deployment.yaml```| ```Deployment``` | Deployment configuration for Gerrit |
|```persistentVolumeClaim.yaml```| ```PersistentVolumeClaim``` | Persistent volume claim configuration |
|```service.yaml```| ```Service``` | ClusterIP service for Gerrit |

The initContainer is responsible for:
- Add LDAP configuration to Gerrit
- Configuring Gerrit to talk to a MySQL Database which is spun up as part of the base chart (Please see README.md at the root of the repository)

## Installing This Chart
This chart is installed as part of the base chart but can also be installed individually.

If installing this chart **independently**, please ensure that the following exist in the namespace already:
- A config map called ```ldap-properties``` which contains values detailed in the ```ldap/``` section of this repository
- A secret called ```gerrit-mysql``` which contains the following keys (This can also be used with an external database):
  - ```mysql-user```: The user for the mysql database
  - ```mysql-password```: The password to the mysql database
  - ```mysql-database```: The mysql database name to use for Gerrit
  - ```mysql-port```: The port for the mysql database
- A secret called ```ldap-secret``` which contains values detailed in the ```ldap/``` section of this repository

Once the above is present, to install with the default settings present in the current level ```values.yaml```:
```sh
helm upgrade gerrit --install .
```

To install and override the persistent value setting to ```false```, use the below: 
```sh
helm upgrade --set persistence.enabled="false" gerrit --install .
```

Or provide your own ```values.yaml``` file:
```sh
helm upgrade -f values.yaml gerrit --install .
```

## Configuration

The following configuration options are available when spinning up this chart. The default Gerrit values are configured so that the service can talk to LDAP that is part of the base chart.

|Parameter|Description|Default|
|---------------------------|-----------------------------------|----------------------------------------------------------|
|```name```| The name of the container created inside the pod | Nexus |
|```replicaCount```| The number of replicas/pods of nexus to have. | ```1``` |
|```restartPolicy```| ```Always```, ```OnFailure```, and ```Never``` | ```Always``` |
|```image.name```| The name of the Docker image to pull down | ```gerritcodereview/gerrit``` |
|```image.tag```| The tag/version of the ```image.name``` to pull down | ```3.1.4``` |
|```image.pullPolicy```| Rule for when to pull the docker image | ```Always```|
|```image.containerPorts```| The container ports to expose |```http: "8080", ssh: 29418```|
|```image.initEnv```| The environment variables used by the init container| |
|```image.env```| The environment variables used by the runtime container| |
|```resources.requests.cpu```| CPU cores requested by Pod | ```150m```|
|```resources.requests.memory```| Memory requested by pod | ```256Mi``` |
|```gerrit_roles.*```| The Gerrit role mappings using url encoding | ```Administrators, Developers, Readers```|
|```service.internalPort```| The port (usually container port) which the service will listen to | ```8080``` |
|```service.externalPort```| The external port which the service will expose  | ```8080``` |
|```persistence.enabled```| Set to ```true``` to enable persistent storage for the Deployment | ```true``` |
|```persistence.accessMode```| The access mode for the Persistent Storage (```ReadOnly```,```ReadWriteOnce```,```ReadWriteMany```) | ```ReadWriteMany``` |
|```persistence.size.etc```| The size of the etc persistent storage. | ```5Gi``` |
|```persistence.size.git```| The size of the git persistent storage. | ```10Gi``` |
|```persistence.size.db```| The size of the db persistent storage. | ```5Gi``` |
|```persistence.size.cache```| The size of the cache persistent storage. | ```1Gi``` |
|```persistence.size.index```| The size of the index persistent storage. | ```5Gi``` |
|```persistence.storageClass```| Storage class to be used with the Persistent Storage | ```-```|

All of the above are configurable in the ```values.yaml``` file.