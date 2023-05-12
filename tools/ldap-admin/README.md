# LDAP Admin Console

This chart deploys the LDAP Administrative Console on a pre-existing Kubernetes cluster.

- [`0.7.1` (*0.7.1/Dockerfile*)](https://github.com/osixia/docker-phpLDAPadmin/blob/v0.7.1/image/Dockerfile)

## Prerequisites Details

- Kubernetes 1.6+

## Chart Details

|File|Type|Description|
|---------------------------|-----------------------------------|----------------------------|
|```deployment.yaml```| ```Deployment``` | Deployment configuration for the LDAP Console |
|```service.yaml```| ```Service``` | ClusterIP service for the LDAP console |



## Installing This Chart
This chart is installed as part of the overall DevTools chart but can also be installed individually. By default it is set to point to the "ldap" service running on the cluster (in the same namespace)

Installing the chart (will use the ```values.yaml``` at this folder level)
```sh
helm upgrade ldap-admin --install .
```

Installing this chart and passing in the LDAP hostname parameter
```sh
helm upgrade --set ldap.host="#PYTHON2BASH:[{'my-other-ldap': [{'server': [{'tls': False}]}]}]" ldap-admin --install .
```

Installing this chart using a ```values.yaml``` file
```sh
helm upgrade -f values.yaml ldap-admin --install .
```

## Configuration

The following configuration options are available when spinning up this chart. The default LDAP values are configured so that the Nexus service can talk to the LDAP chart that is part of the DevTools offering.

|Parameter|Description|Default|
|---------------------------|-----------------------------------|----------------------------------------------------------|
|```Name```| The name of the container created inside the pod | ```ldap-admin``` |
|```replicaCount```| The number of replicas/pods of nexus to have. | ```1``` |
|```restartPolicy```| ```Always```, ```OnFailure```, and ```Never``` | ```Always``` |
|```image.Name```| The name of the Docker Image to pull down | ```osixia/phpldapadmin``` |
|```image.Tag```| The tag/version of the ```Image.Name``` to pull down | ```0.7.1``` |
|```image.PullPolicy```| Rule for when to pull the docker image | ```Always```|
|```image.ldapAdminContext```| The context to run the console on |```/phpldapadmin```|
|```image.containerPorts```| The container ports to expose |```http: "80";https: "443"```|
|```ldap.host```| The hostname of the backend LDAP server to connect to | ```#PYTHON2BASH:[{'ldap': [{'server': [{'tls': False}]}]}]```|
|```resources.requests.cpu```| CPU cores requested by Pod | ```50m```|
|```resources.requests.memory```| Memory requested by pod | ```128Mi``` |
|```resources.limits.cpu```| CPU core limit for Pod | ```200m``` |
|```resources.limits.memory```| Memory limit for Pod | ```512Mi``` |
|```service.internalPort```| The port (usually container port) which the service will listen to | ```80``` |
|```service.externalPort```| The external port which the service will expose  | ```80``` |

All of the above are configurable in the ```values.yaml``` file.