# Nexus

This chart deploys the Nexus Repository Manager on a pre-existing Kubernetes cluster.

- [`3.22.1` (*3.22.1/Dockerfile*)](https://github.com/sonatype/docker-nexus3/blob/3.22.1/Dockerfile)

## Prerequisites Details

- Kubernetes 1.6+

## Chart Details

|File|Type|Description|
|---------------------------|-----------------------------------|----------------------------|
|```init/init.yaml```| ```ConfigMap``` | Contains a shell script which runs in the initContainer to configure LDAP and configure Nexus before startup. |
|```init/ldap_config.yaml```| ```ConfigMap``` | Config map containing JSON payloads for LDAP configuration. This is posted to the Nexus REST API to configure LDAP auth |
|```init/secret.yaml```| ```Secret``` | Randomly generated password for the Nexus admin user.|
|```deployment.yaml```| ```Deployment``` | Deployment configuration for Nexus |
|```persistentVolumeClaim.yaml```| ```PersistentVolumeClaim``` | Persistent volume claim configuration |
|```service.yaml```| ```service``` | ClusterIP service for Nexus |

When this chart is installed, an initContainer is run before Nexus is deployed. The configuration required by the initContainer is kept in the ```init``` folder.

The initContainer is responsible for:
- Changing the default Nexus admin password, to a value stored in k8s secrets.
- Adding LDAP auth configuration
- Mapping the developer, deployment and admin Nexus groups to LDAP groups

The Nexus Admin password for the ```admin``` user can be obtained by:
```sh
kubectl get secret nexus-admin-password -o jsonpath="{.data.password}" | base64 --decode
```


## Installing This Chart
This chart is installed as part of the overall DevTools chart but can also be installed individually.

If installing this chart **independently**, please ensure that the following exist in the namespace already:
- A config map called ```ldap-properties``` which contains values detailed in the ```ldap/``` section of this repository
- A secret called ```ldap-secret``` which contains values detailed in the ```ldap/``` section of this repository

Once the above is present, to install with the default settings present in the current level ```values.yaml```:
```sh
helm upgrade nexus --install .
```

To install and override the persistent value setting to ```false```, use the below: 
```sh
helm upgrade --set persistence.enabled="false" nexus --install .
```

Or provide your own ```values.yaml``` file:
```sh
helm upgrade -f values.yaml nexus --install .
```

## Configuration

The following configuration options are available when spinning up this chart. The default LDAP values are configured so that the Nexus service can talk to the LDAP chart that is part of the DevTools offering.

|Parameter|Description|Default|
|---------------------------|-----------------------------------|----------------------------------------------------------|
|```name```| The name of the container created inside the pod | Nexus |
|```replicaCount```| The number of replicas/pods of nexus to have. | ```1``` |
|```restartPolicy```| ```Always```, ```OnFailure```, and ```Never``` | ```Always``` |
|```image.name```| The name of the Docker image to pull down | ```sonatype/nexus3``` |
|```image.tag```| The tag/version of the ```image.name``` to pull down | ```3.22.1``` |
|```image.pullPolicy```| Rule for when to pull the docker image | ```Always```|
|```image.nexusContext```| The context to run Nexus on |```/nexus```|
|```image.containerPorts```| The container ports to expose |```http: "8081"```|
|```resources.requests.cpu```| CPU cores requested by Pod | ```150m```|
|```resources.requests.memory```| Memory requested by pod | ```256Mi``` |
|```resources.limits.cpu```| CPU core limit for Pod | ```1000m``` |
|```resources.limits.memory```| Memory limit for Pod | ```2048Mi``` |
|```service.internalPort```| The port (usually container port) which the service will listen to | ```8081``` |
|```service.externalPort```| The external port which the service will expose  | ```8081``` |
|```persistence.enabled```| Set to ```true``` to enable persistent storage for the Deployment | ```true``` |
|```persistence.accessMode```| The access mode for the Persistent Storage (```ReadOnly```,```ReadWriteOnce```,```ReadWriteMany```) | ```ReadWriteMany``` |
|```persistence.size```| The size of the persistent storage. | ```20Gi``` |
|```persistence.storageClass```| Storage class to be used with the Persistent Storage | ```standard```|
|```persistence.volumeMode```| The mode for the volume | ```Filesystem``` |

All of the above are configurable in the ```values.yaml``` file.