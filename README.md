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


