## Create ReplicaSet
kubectl create -f replicaset-demo.yml

## replicaset-demo.yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-helloworld-rs
  labels:
    app: my-helloworld
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-helloworld
  template:
    metadata:
      labels:
        app: my-helloworld
    spec:
      containers:
      - name: my-helloworld-app
        image: stacksimplify/kube-helloworld:1.0.0

## List ReplicaSets
kubectl get replicaset
kubectl get rs

kubectl describe rs/<replicaset-name>

kubectl describe rs/my-helloworld-rs
[or]
kubectl describe rs my-helloworld-rs

kubectl get pods
kubectl describe pod <pod-name>

# Get list of Pods with Pod IP and Node in which it is running
kubectl get pods -o wide

* Verify under "name" tag under "ownerReferences". We will find the replicaset name to which this pod belongs to.

kubectl get pods <pod-name> -o yaml
kubectl get pods my-helloworld-rs-c8rrj -o yaml 

# Expose ReplicaSet as a Service
kubectl expose rs <ReplicaSet-Name>  --type=NodePort --port=80 --target-port=8080 --name=<Service-Name-To-Be-Created>
kubectl expose rs my-helloworld-rs  --type=NodePort --port=80 --target-port=8080 --name=my-helloworld-rs-service

# Get Service Info
kubectl get service
kubectl get svc

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
Access the Application using Public IP
http://<node1-public-ip>:<Node-Port>/hello
<!-- 
Whenever a POD is accidentally terminated due to some application issue, ReplicaSet should auto-create that Pod to maintain desired number of Replicas configured to achive High Availability.
 -->
# To get Pod Name
kubectl get pods

# Delete the Pod
kubectl delete pod <Pod-Name>

# Verify the new pod got created automatically
# Before change
spec:
  replicas: 3

# After change
spec:
  replicas: 6

# Apply latest changes to ReplicaSet
kubectl replace -f replicaset-demo.yml

# Verify if new pods got created
kubectl get pods -o wide

# Delete ReplicaSet
kubectl delete rs <ReplicaSet-Name>

# Sample Commands
kubectl delete rs/my-helloworld-rs
[or]
kubectl delete rs my-helloworld-rs

# Verify if ReplicaSet got deleted
kubectl get rs
Delete Service created for ReplicaSet
# Delete Service
kubectl delete svc <service-name>

# Sample Commands
kubectl delete svc my-helloworld-rs-service
[or]
kubectl delete svc/my-helloworld-rs-service

# Verify if Service got deleted
kubectl get svc