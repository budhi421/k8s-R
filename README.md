Kubernetes architecture:
---------------------------------------

When we deploy an application in a container we need  mission to run those containers, those mission can be virtual or physical In the kubernetes world we call these mission as nodes more specically worker nodes as most of the work is done by these nodes .
Generally there will be multiple worker nodes so that if one nodes goes down containers can be run in other nodes also we can run the same application on 
multiple nodes to shared the load we call these set of worker node as data plane .Now these should be manage by worker nodes 

For example If one node goes down all pods moves to a healthy node etc.This controling part is taken by another node called master node or called control plane .In real time theare will be more than one master node for the fault tolerence 
worker node and master nodes together form a cluster.

So a kubernetes cluster consist of a set of worker mission called worker nodes that run the containerised application and a set of master nodes which manages thease worker node.
In production environment the control plane usually runs across multiple containers and a cluster usually runs multiple nodes providing fault tolerance and high availability .

---------------------------------------
So how do master and worker nodes differ what component does a master node have and what does a worker node have.
Let us start with master node component , master node consist of kubernetes components that control the cluster along with the data about the cluster state and configuration.
First component is 

1> Kube-api server:  
...................
End user can interact with thease api directly or through CLI , this api called as front-end for the kubernetes control plane. with this api we can instruct kubernetes to do some operations like scheduling pod , get list of the pod etc.

2> etcd:
.......
Theare should be some storage where we will track all the nodes that we have in the cluster what pods or containers we have and their state etc.
etcd is a simple key value store used to store cluster data , It is recommended to have a backup plan for the etcd so that we can restore on any failures.
It is only accessible from the api server for security reasons no-other component can interact directly with etcd except api-server.
etcd has a wonderful feature called watch api , this watch waits for the changes to keys by continuously watching and send those key updates back to the client. So if any changes happens in the etcd records kubernetes api will respond accordingly.

3> kube-scheduler:
.................
It helps to schedule the pods on the various nodes based on the resources utilization for example If our application needs 2GB of memory and 2GB CPU core 
then the pods for that application will be scheduled on a node with atlease those resources . 
Once the node is selected by the scheduler it will call the api-server.

4> Kube-controller:
..................
when a change in a service configuration occurs for example replacing the image from which the pods are running or changing parameters in the configuration 
in yaml file ,The controller pods change and start working towards the new desired state.
There and different types of controller available
replication controller
node controller
endpoint controller etc
replication controller - ensure the current number of pods are running in the cluster 
node controller        - monitors the health of each node and notify the cluster when nodes come online or become unresponsive.
endpoint controller    - connect the pods and services to populate the endpoint object


------------------------------------------------------------------------------------------------
Node component:

node component run on every node maintaining running pods and providing the kubernetes runtime environment .
The first node component is 
container runtime:  To run a container from an image we need a container runtime there are many container runtime availabe in the market like racket , docker , etc . Docker is most popular container runtime , The container is a software that is responsible for running continuous 

Kubelet: This ia an agent that runs on each worker node in the cluster It makes sure that the containers are running in a pod ,It regularly checks the new or modified pod specification from the api-server and ensuring the pods and their containers are healthy and running in the desired state.
The kubelet does not manage the containers that are not created by kubernetes and it sending all the state to control plane.

kube-proxy:
kube-proxy is a network proxy that runs on each worker node in your cluster implementing pods of the kubernetes service concept. When a request is recieved
for your application it make sure to forward your request to a appropriate pods 



summary:

control plane is in constant contact with your computing mission 
               -----OR-----
worker nodes to make sure your cluster is running based on the configuration you have provided.


Lets we want to create two instances of an application first we give that configuration or spec in a document once spec is ready we send it to the api-server 
directly or through CLI ,the api then runs the spec by the scheduler,the scheduler selects the worker node to which new node should be assigned based on the 
configuration and resource availability , at the same time master server also store the configuration and status data to etcd which is the key value store.
Once the scheduler assigns a worker node the controller manager on the master node sends an object spec to the node via kubernetes api so it can create the 
desired object , upon recieving the object spec the kubelet on the node ensures the objects are created accordingly.whenever the status of the pods is changed
like pod is killed the kubelet via api update the etcd on master with the object status.
Once our application is deployed we want to make a request to our application kube-proxy takes our request and forward our rquest to corresponding pods.
 
 
