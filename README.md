# Kubernetes

![K8s](https://user-images.githubusercontent.com/36560845/108598544-5ad4bc00-73b8-11eb-95ae-df6c5c8f46af.png)

## Features
   - High availability or, no downtime
   - Scalibility or high performance
   - Disaster recovery i.e. backup and restore

## Kubernetes Components
   - **Pod:** 
     * Smallest unit of kubernetes
     * Abstraction over container. In docker, containers are the abstraction over images. But in k8s, pods are the abstraction layer over containers.
     * 1 application per pod. Can have multiple apps per pod if there are dependencies to run one app. But, usually 1 app per pod.
     * Own IP per pod. If pod destroys, new IP gets assigned.

   - **Service**
       * Services are the permanent IP addresses that can be attached to a pod. If a pod destroys, services are not changed. 
       * If there are 4 pods with same service name, when service receives a request, it'll balance the requests to the 4 pods. 

#### ReplicaSets
```kubectl create -f replicaset-definition.yml```

```kubectl get replicaset```

```kubectl delete replicaset myapp-replicaset [It also dletes underlying pods]```

```kubectl replace -f replicaset-definition.yml```

```kubectl scale --replicas=6 -f replicaset-definition.yml ```

## Commands
   - kubectl run hello-minicube
   - kubectl cluster-info
   - kubectl get nodes
   - kubectl run nginx --image nginx
   - kubectl get pods
### pod-definition.yml
   ```
   apiVersion: v1
   kind: Pod
   metadata: <------------------ dictionary
     name: myapp-pod
     labels: <------------------ dictionary
       app: myapp
       type: frontend
   spec:
     containers: <------------- list
     - name: nginx-container <----- first element of list
       image: nginx
   ```
   - To create pod from above pod-definition, run ``` kubectl create -f pod-definition.yml ```
   - To view details of this pod, run ``` kubectl describe myapp-pod ```
   - A Note on Editing Existing Pods
      In any of the practical quizzes if you are asked to edit an existing POD, please note the following:

      If you are given a pod definition file, edit that file and use it to create a new pod.
      If you are not given a pod definition file, you may extract the definition to a file using the below command:
      ``` kubectl get pod <pod-name> -o yaml > pod-definition.yaml ```

      Then edit the file to make the necessary changes, delete and re-create the pod.

      Use the ``` kubectl edit pod <pod-name> ``` command to edit pod properties.
### Replication Controller & Replicaset
    - To scale existing replicaset, run the following:
      ``` kubectl replace -f replicaset-definition.yml ```
      ``` kubectl scale --replicas=6 -f replicaset-definition.yml <--- number of replicas will not be updated in yml file. but pods will be created/scaled```
      ``` kubectl scale --replicas=6 replicaset myapp-replicaset```
      
### Edit image name of pods inside an existing replicaset:
    - ``` kubectl edit rs new-replica-set```
    - edit the image name
    - delete old pods ``` kubectl delete pod pod1 pod2 pod3 ```
    - As we edited the rc.yml file, the rc will be created as well as pods with the new updated image.
    
 ### scale replicaset to 5 pods
     - ``` kubectl scale replicaset new-replica-set --replicas=5```
     - OR we can just edit the rc.yml file. replicas will be automatically scaledup/scale down
     
### switch to another namespace permanently
    - ``` kubectl config set-context $(kubectl config current-context) --namespace=dev ```
    - to view pods of default, ``` kubectl get pods --namespace=default``` ``` kubectl get pods --namespace=prod```
    
### connect to a service of different namespace: 
    - ``` app1.connect("service-name.namespace_name.svc.cluster.local") ```
    

### Certification tips

Certification Tip – Imperative Commands!
While you would be working mostly the declarative way – using definition files, imperative commands can help in getting one time tasks done quickly, as well as generate a definition template easily. This would help save considerable amount of time during your exams.

Before we begin, familiarize with the two options that can come in handy while working with the below commands:

--dry-run: By default as soon as the command is run, the resource will be created. If you simply want to test your command , use the --dry-run=client option. This will not create the resource, instead, tell you whether the resource can be created and if your command is right.

-o yaml: This will output the resource definition in YAML format on screen.

 

Use the above two in combination to generate a resource definition file quickly, that you can then modify and create resources as required, instead of creating the files from scratch.

 

POD
Create an NGINX Pod

kubectl run nginx --image=nginx

 

Generate POD Manifest YAML file (-o yaml). Don’t create it(–dry-run)

kubectl run nginx --image=nginx --dry-run=client -o yaml

 

Deployment
Create a deployment

kubectl create deployment --image=nginx nginx

 

Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run)

kubectl create deployment --image=nginx nginx --dry-run -o yaml

 

Generate Deployment with 4 Replicas

kubectl create deployment nginx --image=nginx --replicas=4

 

You can also scale a deployment using the kubectl scale command.

kubectl scale deployment nginx --replicas=4

 

Another way to do this is to save the YAML definition to a file and modify

kubectl create deployment nginx --image=nginx--dry-run=client -o yaml > nginx-deployment.yaml

 

You can then update the YAML file with the replicas or any other field before creating the deployment.

 

Service
Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379

kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml

(This will automatically use the pod’s labels as selectors)

Or

kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml (This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

 

Create a Service named nginx of type NodePort to expose pod nginx’s port 80 on port 30080 on the nodes:

kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml

(This will automatically use the pod’s labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml

(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

 

Reference:

https://kubernetes.io/docs/reference/kubectl/conventions/




### Imparative commands
    - Deploy a redis pod using the redis:alpine image with the labels set to tier=db.
    ``` kubectl run redis --image=redis:alpine --dry-run=client -oyaml > redis-pod.yaml ```
     ``` kubectl create -f redis-pod.yaml ```
     
     OR, ``` kubectl run redis -l tier=db --image=redis:alpine ```
     
    - Create a service redis-service to expose the redis application within the cluster on port 6379.
    ``` kubectl expose pod redis --port=6379 --name redis-service ```
    - Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas.
    ``` kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3 ```
