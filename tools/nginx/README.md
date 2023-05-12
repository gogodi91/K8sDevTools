# NGINX

This chart deploys the Nginx Reverse Proxy on a pre-existing Kubernetes cluster.

- [1.17.7` (*1.17.7/Dockerfile*)]()

## Prerequisites Details

- Kubernetes 1.6+

## Chart Details

|File|Type|Description|
|---------------------------|-----------------------------------|----------------------------|
|```init/init.yaml```| ```ConfigMap``` | Start up script for Nginx, initialises configuration |
|```init/config-nginx.yaml```| ```ConfigMap``` | The Nginx proxy configuration to point to downstream tools |
|```init/nginx-run.yaml```| ```ConfigMap``` | Nginx start up script  |
|```deployment.yaml```| ```Deployment``` | Deployment configuration for Nginx |
|```ingress.yaml```| ```Ingress``` | Persistent volume claim configuration |
|```service.yaml```| ```Service``` | ClusterIP service for Nginx |

This chart will run an initContainer which will setup the Nginx configuration files ```default.conf``` and ```nginx.conf```. Please see **Configuration**, since there are some external variables which are set.

## Installing This Chart

## Configuration
This chart has some externally configured values which are dependent on other components of the (Kubernetes) DevTools platform being deployed. Please see the README.md at the root of this folder for the information.

|Parameter|Description|Default|
|---------------------------|-----------------------------------|----------------------------------------------------------|
|```name```| The name of the container created inside the pod | Nginx |
|```replicaCount```| The number of replicas/pods of nexus to have. | ```1``` |
|```restartPolicy```| ```Always```, ```OnFailure```, and ```Never``` | ```Always``` |
|```image.name```| The name of the Docker Image to pull down | ```gogodi91/nginx``` |
|```image.tag```| The tag/version of the ```image.Name``` to pull down | ```1.17.7``` |
|```image.pullPolicy```| Rule for when to pull the docker image | ```Always```|
|```image.env```| The environment variables to substitute into Nginx at runtime |```${LDAP_SERVER} ${LDAP_ACCOUNTBASE} ${LDAP_ROOTDN} ${LDAP_MANAGER_DN} ${LDAP_DISPLAY_NAME_ATTRIBUTE_NAME} ${LDAP_MAIL_ADDRESS_ATTRIBUTE_NAME} ${LDAP_USER_BASE_DN} ${LDAP_GROUP_BASE_DN} ${LDAP_USER_SEARCH} ${LDAP_ACCOUNTPATTERN} ${LDAP_ACCOUNTFULLNAME} ${LDAP_GROUPPATTERN} ${LDAP_GROUPMEMBERPATTERN} ${LDAP_GROUP_NAME_ADMIN}```|
|```image.containerPorts```| The container ports to expose |```http: "8080";https: "8443"```|
|```service.internalPort```| The port (usually container port) which the service will listen to | ```8080``` |
|```service.externalPort```| The external port which the service will expose  | ```80``` |
|```service.relativePath```| The relative path for the service  | ```/``` |
|```ingress.host```| The host for the ingress object | ```""``` |
|```ingress.annotations.ingress.kubernetes.io/rewrite-target```| The context for rewriting | ```/``` |
|```ingress.annotations.ingress.kubernetes.io/whitelist-source-range```| The IPs white-listed for accessing the ingress | ```0.0.0.0/0``` |