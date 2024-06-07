# cost-optimization-gke
#finOps

Firstly created doc which specifies following:
 - Current cost with respect to enviroment dev , Stage and Prod
 - and take a look at the utilization of resources
 - based on utilization of resorces we conclude our desired cost

![image](https://github.com/Shubham2194/cost-optimization-gke/assets/83746560/b1e8d9b6-c729-41dd-9f67-d8a50a74fad3)


Max Billling area found as GKE slave nodes in my case (in your case it can be GCS,VM,Lb etc) 

If you also need to optimize your cost with respect to GKE , Lets Begin !!



Step 1 :
	First Create new node pool (starting with lowest type i.e e2-medium-2) with number of nodes as 1 with boot size as per requirement
 ![image](https://github.com/Shubham2194/cost-optimization-gke/assets/83746560/e78ce62b-8f61-4713-a4ef-2a19dc5022e2)

 

Note: Make sure to add  Kubernetes labels  in metadata section according to enviroment 
(env=dev)


![image](https://github.com/Shubham2194/cost-optimization-gke/assets/83746560/d28ec4ae-a295-42d5-8897-30da9005af5d)



Step 2 : Schedule the running microservices to newer node
- Head over to the Manifest file of microservice and add nodeaffinity section to schedule pod on the new node



![image](https://github.com/Shubham2194/cost-optimization-gke/assets/83746560/45034d40-1293-48c8-9495-b7e69cc6c993)



Step 3: Apply the changes
- Either apply the changes directly on k8s env or Run the CI/CD (if configured)
- Look after the pods in the Cluster

  Kubectl get pods -w
  
  kubectl get pods -o wide (see the nodes where is it scheduled)


 Step 4 : Look at the  READINESS GATES and LIVENESS GATES
 - if everything works as per expectation  and pod has been scheduled to the newwe node ,we are good to go with step 5 , if not then headover to step 1 and resize the node pool to bit higer configuration.

Step 5: Cordon the node 

 - kubectl cordon  < node name >
 
 This will make your Node cordon , which do as follows

* New pods are not scheduled on the node.
  
* Existing pods continue to run.

Step 6 : Drain and delete the Node

- kubectl drain < node name > --ignore-daemonsets --delete-emptydir-data
 
 This will drain you node , which do as follows


* Evicts all pods from the node, except for DaemonSets and mirror pods.
  
* Pods are gracefully terminated and rescheduled on other nodes, if presnet on the node.
  
- kubectl delete node <node name>


![image](https://github.com/Shubham2194/cost-optimization-gke/assets/83746560/8f9e2495-47a2-4628-9e14-8073fd6ce46f)

 
	
DONE , We lower down 80% cost !!
Thanks for reading !!
