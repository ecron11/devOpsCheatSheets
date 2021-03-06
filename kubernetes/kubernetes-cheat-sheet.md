#  Kubernetes Cheat sheet
## A Cheat sheet for kubernetes and Kubernetes related topics

### Concepts - Basic
#### Node
A Node is a machine where containers will be launched by K8s.

#### Cluster
A group of nodes. Having a cluster, means that one machine failing will not take down the whole application

#### Master Node
A node that watches over and orchestrates the other nodes in a cluster

#### Pods
A encapsulation of a container in Kubernetes. A pod is the smallest object that can be created in kubernetes. In kubernetes, you do not interact directly with a container; instead you interact with pod.

#### Scaling with Pods
To scale up or down, additional pods are deployed. You do **not** add additional containers into a pod. Instead, scaling involves adding more pods into nodes

#### Multi-container Pods
A pod represents a specific piece of an application or infrastructure. This means that often a pod encapsulates on container. However, this is not a requirement. Multiple containers can be in a pod, but they should be different types of containers. Multiple containers in a pod are linked and share many things, I.E. network, storage, fate (when a pod is destroyed, all the containers in it are destroyed).  
Multi-pod containers are a **rare use case**

#### Controllers
Processes that monitor Kubernetes objects and respond accordingly

#### Replication Controller
A controller that monitors a pod or set of pods

#### Replica Set
A newer replacement for replica controllers

#### Replica Set vs Replication Controller
In a replica set, the selector is required to be specified. Where in replica controller it is optional. A replicaset will maintain the number of replicas in it. If new ones are created, they will be deleted by default and if there is not enough, they will be deleted.

#### Deployments
A deployment is a collection of other objects in the hierarchy, such as replicasets

#### Deployment Strategy
Rolling update is the default deployment strategy, where replicas are taken down and replaced one by one.

#### Service
A kubernetes object for connecting different parts of the kubernetes infrastructure together. A service functions like a server with a very specifc function in kubernetes with it's own internal ip address.

#### NodePort
A node in kubernetes has it's own network. If you were to ssh into the node, you could access the pods Ip addresses. However, these are internal to the node, and by default are inaccessible to external requests. A **nodeport**, can translate a port on the **node** to an node-internal ip address attached to a **pod**.  
A NodePort can be used for multiple pods. In this case, it will randomly distribute the load across the mulitple pods like a built-in load balancer. If the pods are in multiple nodes, the service will span all of them. In this case you can access the nodes from the NodePort using any ip that the NodePort spans alond with the port specified.

#### Cluster Ip
Service that creates a virtual IP inside the cluster attached to a group of pods based on a selector. Provides a single, consistent endpoint that can be used to represent a service.

#### Network Policy
By default in Kubernetes, all kubernetes objects are able to interact with any other objects in the cluster with an allow all rule. A network policy is a kubernetes object that can be attached to pods in kubernetes that dictates what can be allowed into and out of an object.

#### Pod Selector
To use AND logic in pod selectors, use a dictionary in the YAML file. Rules included under a dash will be treated as a AND. Separate rules with their own dash are treated as an or.

#### Load Balancer
Service that creates a load balancer

#### Namespaces
A namespace is a group of kubernetes objects seperated from other namespaces. Some features of namespaces include: 
- A namespace can have it's own resource limits.
- A namespace can have it's own policies.
- Objects in a namespace can reach other objects in the same namespace with just the name of the pod. Objects can also reach other namespaces by appending the name of the namespace

A namespace can be specified for commands. Such as get pods, which by default only gets pods in the default namespace.  

A namespace can also be specified in definition fiels for kubernetes objects.  

To set a resource quota for a namespace, create a ResourceQuota Object

#### -o flag
The -o flag can be added to any kubectl command to change the output format. The default is human-readable plain text format. There are 4 options:
- json - json format
- name - resource name and nothing else
- wide - output in plain text with additional information
- yaml output YAML formatted API object

#### Arguments and Entry points
Arguments are added to an entrypoint (A initial command that is run when a container is created) when a container is created. To add arguments to the entrypoint in Kubernetes, the args filed can be set to an array of arguments under the container in the pod-definition file. To overwrite the entry point, set the commmand field

#### Environment Variables
To set an environment variables in Kubernetes definition files, set env field to yaml array of name: value

#### ConfigMaps
A set of key value pairs to be used in pod definition files.

#### Secrets
Like a configmap, but stored in a hash. When creating a secret definition file, you must specify the secrets in base 64 encoded format. To get this on a linux host use command echo -n <stringToEncode> | base64

#### Docker Security

##### Process Isolation
Within a container, only the processes on the container itself (AKA within it's own namespace) are visible. On the docker host (The machine running docker that all the containers sit on top of), all processes in child containers/namespaces are visible

##### Users
By default, all commands run on a container are run with the root user. If desired, the user can be specified

##### Root User in docker containers
By defualt, the root user does not have some capabilities, such as rebooting the host. These capabilities can be added with the --cap-add flag in docker run and dropped with --cap-drop flag. To add all priveleges use --privileged flag

#### Security Contexts
Let's you specify docker security concepts in the pod definition. Can set at either the pod level or the container level. Security Contexts at the container level will override security at the pod level.

##### Capabilities
Capabilities in the way docker containers use it can be added/removed at the container level only. With capabilities flag.

#### Resource Requirements
- By default kubernetes assumes that a container in a pod requires .5 cpu and 256 MiB of memory. This is called it's resource requests. A resource request is the minimum amount of a resource that is reserved for a container in a pod To change it, specifiy it under resources: requests in the container definition.  
- By default, kubernetes sets a limit of  vCPU per container and 512 MiB of memory per container. This is the maximum amount of the resources that a container in a pod is allowed to use. This can be changed in the pod definition file with the limits key under resources. If a container tries to use more CPU than it's limit, it will be throttled. However, if a container tries to use more Memory, it can go over. If it goes over consistently, it will be terminated.

#### User accounts and service accounts
User Accounts are used by humans and service accounts are used by processes. When a service account is created, a token is created automatically. It is what must be used by the external service to communicate with the api. The token is stored in a secret object. If the service is an application deployed in the kubernetes cluster, it can be mounted as a volume onto the service. That way the token is already placed inside the pod and can be easily read by the application.  
Whenever a pod is created, the default service account and it's token is automatically mounted onto the pod by default.

#### Taints and Tolerations
A taint prevents pods that do not have a matching toleration from deploying to that node. Ex. A node has the "blue" taint. Only pods that have the "blue" toleration can deploy to it. Taints and tolerations do not tell a pod where to go. It simply says that only pods with a certain toleration can be deployed there. A pod could be deployed anywhere it is tolerated. Node affinity is used to specify where a pod should be deployed

##### Taint Effects
A taint effect determines what happens to a pod that does not tolerate the taint. There are 3 types:
- NoSchedule: The pod will not be scheduled on the node
- PreferNoSchedule: System will try to not scheudle a pod on the node
- NoExecute: New pods that do not tolerate the taint will not be scheduled AND existing pods that do not tolerate the taint will be evicted.


#### Node Selectors
Node Selectors let you specify what nodes to deploy a pod to based on node labels. It is a very simple and relatively limited way to specify nodes in kubernetes. You cannot do complex rules like or or not. For more complex rules, node affinity would be required

#### Node Affinity
Node Affinity allows you to specify what node(s) you want your containers to deploy on. It allows more complex rules like exists, in, notIn, etc. There are currently 2 types of affinity available:
- requiredDuringSchedulingIgnoredDuringExecution: If a pod cannot be placed on a node with the correct label, it will not be placed
- preferredDuringSchedulingIgnoredDuringExecution: If a pod cannot be placed on a node with the correct label, it will ignore affinity rules

#### Pod lifecyle
States of a pod:
- Pending: When the scheduler is attempting to figure out where to put a pod
- ContainerCreating: A pod has been scheduled on a node, images are pulled and the container starts
- Running: The pod is running

Pod Conditions are an array of true/false values that describe the status of a pod. They are:
- PodScheduled
- Initialized
- ContainersReady
- Ready

##### Readiness
By default, Kubernetes assumes that a pod is ready once it's containers have been created. This could be an issue if the application inside of it isn't actually ready. Kubernetes will send traffic to it, even if it is not ready to accept it.

#### Readiness Probe
A readiness probe is a custom test for when a pod is ready to accept traffic. Kubernetes will only forward traffic once this test is passed positively. This probe can take the form of an HTTP GET request, a check for whether a port is listening, or execution of a custom script on the container.

#### Liveness Probe
A liveness probe, is a test that is sent to a container periodically to determine if it healthy. If a container is considered to be unhealthy, it is destroyed and recreated. Liveness probes are configured in the same way readiness probes are.

#### Labels
Labels in kubernetes are just key-value pairs that can be defined. They can be used to filter views, selected from Deployment groups, etc. Using the -l flag on kubectl get <object> command let's you view object types with the label specified.

#### Jobs
The default behavior of a pod in kubernetes is to attempt to keep a container running, something like a web-server, or database. If a container exits, kubernetes will try to bring it back up repeatedly until the failure threshold has been met. This behavior is controlled by the restartPolicy key in the kubernetes definition file. To create multiple pods that run a task to completion, kubernetes jobs come in. They are like replicasets for pods that don't need to run indefinitely.  
A job will attempt to create the number of completions specified until it has had the same number of successes. The number of pods running at the same time can be set with the parallelism key.

#### Ingress
Like a layer 7 load balancer that is built into the kubernetes cluster. An Ingress controller is a reverse proxy that can forward requests to the right pods, cluster IPs, etc from outside the cluster. Also need a NodePort Service to expose the Nginx service to the external web

#### Volumes
Volumes can be defined and mounted to pods for data that needs to persisent. hostPath defines a directory on the host node. Not good for multi-node clusters because nodes will have different things at the defined directories. <br/>
There are several other different types of volume types that can be defined, such as NFS, AWS EBS, etc.

#### Persistent Volumes
A cluster wide pool of volumes used by resources across the cluster. This is advantageous over volumes at the pod level because you can more centrally manage your volumes. If there is a change to the volumes that are defined at the pod level, every pod definition must be changed, however with a persistent volume, any changes to the volume is done in the volume object.

#### Persistent Volume Claims
A Persistent Volume Claim is a request to bind to a persistent volume. There is a one-one relationship between persistent volumes and persistent volume claims. By default, once a persistent volume claim has been deleted, the persistent volume it was attached to is retained. This means that it stays, but cannot be re-used by other volume claims. The other options are delete, which deletes the volume when it is used, and recycle which clears out the persistent volume and re-uses it.

#### Storage Classes
A storage class let's you define a type of storage that can be referenced in a persistent volume claim. This will automatically create a persistent volume of the requested size from the type of resource (such as an EBS volume in AWS) specified in the storage class object.

#### Stateful Sets
Deploy a group of pods like deployments, but with some differences:
- They deploy pods sequentially instead of simultaneously
- The pods have definitive names, not randomized ones like in deployments. The names are the name of the stateful set and the index of the pod. name-0, name-1, etc. 
- Even if a pod fails, and is recreated, it will come up with the same name.

#### Headless Services
Like a ClusterIP service, but instead of load-balancing, gives a DNS address to each pod in a service. With a normal service, you can access the IP adress/DNS name for the service and it will be sent to a random pod under it, a headless service will let you adress each specific pod, even if they fail and are recreated. This can be useful for things like mySQL databases that have read and write pods.

### Commands - Basic
```bash
kubectl get all
```
Lists all objects in cluster

```bash
kubectl get nodes
```
Lists nodes in cluster
-o wide gives more information

```bash
kubectl run <podname> --image=<image_name>
```
Creates a pod with a specific image

```bash
kubectl get po
```
Lists pods. Can also use 'get pods'

```bash
kubectl describe pod <podname>
```
Gives details of a specific pod

```bash
kubectl apply -f <config_filepath>
```
Creates a pod(or other object) using a config file

```bash
kubectl get replicationcontroller
```
Shows all replication controllers

```bash
kubectl get replicationsets
```
Shows all replication controllers

```bash
kubectl replace -f <config path>
```
Replaces a config with a newer version

```bash
kubectl scale --replicas=6 replicaset myapp-replicaset
```
Scales the number of replicas in a set to a specified number

```bash
kubectl edit <object> <object name>
```
Opens a text editor to edit an object

```bash
kubectl rollout status <deployment>
```
Check on the status of a deployment
web-dashboard
```bash
kubectl rollout history deployment/<deploymentName>
```
Show the history of a rollout

```bash
kubectl rollout undo <deployment>
```
Undo a deployment

```bash
kubectl get svc
```
Get Services

### Commands - Intermediate
```bash
kubectl get pod <pod-name> -o yaml > pod-definition.yaml
```
Outputs an existing pod's definition to a YAML file

```bash
kubectl config set-context ${kubectl config current-context} --namespace=dev
```
Changes the namespace

```bash
kubectl create configmap <configmap-name> --from-literal=<ENV_VAR_NAME>=<ENV_VAR_VALUE>
```
Creates a config map imperatively, without a configmapfile

```bash
kubectl create configmap <configmap-name> --from-file=<path-to-file>
```
Creates a configmap from a file

```bash
echo -n <valueToEncode> | base64
```
returns a base64 encoded secret

```bash
kubectl create secret generic <secretName> --from-literal=<Secret1Key>=<Secret1Value>
```
Creates a secret imperatively

```bash
kubectl exec -it  <pod> -- <command>
```
Executes a command on a pod

```bash
kubectl create serviceaccount <serviceAccountName>
```
Creates a service account

```bash
kubectl taint nodes <node-name> <key>=<value>:<taint-effect>
```
Sets the taint on a node.

```bash
kubectl label nodes <node-name> <label-key>=<label-value>
```
Sets the labels on a node

```bash
kubectl logs <pod-name>
```
Shows the logs on a pod. User -f flag to show logs live. If the pod has multiple containers, you must also specify the name of the container to see the logs

```bash
kubectl top node
```
If metric server is enabled, shows resoure utilization on nodes

```bash
kubectl top pod
```
If metric server is enabled, shows resoure utilization on pods

```bash
kubectl create ingress <ingress-name> --rule="host/path=service:port"
```
Creates an ingress imperatively

### Kubectl Object shortcuts
Shortcut | Object Name
---------|------------
po | Pods
rs | ReplicaSets
deploy | Deployments
svc | Services
ns | NameSpaces
netpol | Network Policies
pv | Persistent Volumes
pvc | PersistentVolumeChains
sa | Service Accounts
### Commands - Minikube
```bash
minikube service <service> --url
```
Gets the url for a service