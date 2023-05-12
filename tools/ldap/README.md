# LDAP

This chart deploys OpenLDAP on a pre-existing Kubernetes cluster.

- [`2.4.44` (*2.4.44/Dockerfile*)](https://bitbucket.org/rbhadti94/docker-openldap/src/master)

## Prerequisites Details

- Kubernetes 1.6+

## Chart Details

|File|Type|Description|
|---------------------------|-----------------------------------|----------------------------|
|```config-modules-ldifs.yaml```| ```ConfigMap``` | Config map containing the ```ppolicy``` LDIF which is used to configure password policies |
|```config-password-config.yaml```| ```ConfigMap``` | Config map containing the password ruleset  |
|```config-prepopulate-ldifs.yaml```| ```ConfigMap``` | Config map containing LDIFs for adding users/groups |
|```ldap-properties.yaml```| ```ConfigMap``` | Config map containing LDAP configuration values (used for other services) |
|```deployment.yaml```| ```Deployment``` | Deployment Configuration for LDAP|
|```secret.yaml```| ```Secret``` | The secret that will hold passwords for all LDAP users |
|```persistentVolume.yaml```| ```PersistentVolumeClaim``` | Persistent Volume for LDAP configuration |
|```service.yaml```| ```Service``` | LDAP service  |

This chart creates 2 objects which will be used by other services:
- ```ldap-secret```: When created will passwords for LDAP users (Randomly generated passwords)
- ```ldap-properties```: Contains LDAP configuration info which is used by other services

## Installing This Chart
This chart is installed as part of the overall DevTools chart but can also be installed individually.

Installing the chart (will use the ```values.yaml``` at this folder level)
```sh
helm upgrade ldap --install .
```

Installing the chart using a custom admin username and account base
```sh
helm upgrade --set image.env.ADMIN_USERNAME="my-other-admin" --set image.env.LDAP_ACCOUNTBASE="dc=otheraccount,dc=company,dc=com"
```

## Configuration

The following configuration options are available when spinning up this chart. The default LDAP values are configured so that the Nexus service can talk to the LDAP chart that is part of the DevTools offering.

|Parameter|Description|Default|
|---------------------------|-----------------------------------|----------------------------------------------------------|
|```Name```| The name of the container created inside the pod | Nexus |
|```replicaCount```| The number of replicas/pods of nexus to have. | ```1``` |
|```restartPolicy```| ```Always```, ```OnFailure```, and ```Never``` | ```Always``` |
|```image.Name```| The name of the Docker Image to pull down | ```rbhadti/openldap``` |
|```image.Tag```| The tag/version of the ```image.Name``` to pull down | ```latest``` |
|```image.PullPolicy```| Rule for when to pull the docker image | ```Always```|
|```image.containerPorts```| The container ports to expose |```ldap: "8389";ldaps: "8636```|
|```image.env.SLAPD_DOMAIN```| The domain for the LDAP server | ```ldap.example.com``` |
|```image.env.SLAPD_FULL_DOMAIN```| The full domain for the LDAP server | ```devtools-admin``` |
|```image.env.SLAPD_ADDITIONAL_SCHEMAS```| The additional schemas to load - by default this is the ppolicy schema | ```ppolicy``` |
|```image.env.SLAPD_ADDITIONAL_MODULES```| The additional modules to load - by default this is the ppolicy module | ```ppolicy``` |
|```resources.requests.cpu```| CPU cores requested by Pod | ```50m```|
|```resources.requests.memory```| Memory requested by pod | ```128Mi``` |
|```resources.limits.cpu```| CPU core limit for Pod | ```200m``` |
|```resources.limits.memory```| Memory limit for Pod | ```512Mi``` |
|```service.internalPort```| The port (usually container port) which the service will listen to | ```389``` |
|```service.externalPort```| The external port which the service will expose  | ```389``` |
|```persistence.enabled```| Set to ```true``` to enable persistent storage for the Deployment | ```true``` |
|```persistence.accessMode```| The access mode for the Persistent Storage (```ReadOnly```,```ReadWriteOnce```,```ReadWriteMany```) | ```ReadWriteMany``` |
|```persistence.size```| The size of the persistent storage. | ```20Gi``` |
|```persistence.storageClass```| Storage class to be used with the Persistent Storage | ```standard```|
|```persistence.volumeMode```| The mode for the volume | ```Filesystem``` |

All of the above are configurable in the ```values.yaml``` file.

## Important Information
This chart will produce 2 resources (a config map and a secret) which will be required by all other services for LDAP integration. These will contain the following values. Please see:
- ```ldap-properties.yaml```
- ```secret.yaml```
For the fields that will be used by the other applications.

If using your own LDAP instance, please ensure that the above resources for your LDAP instance exist.