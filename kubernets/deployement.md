
## Step-02: Create Deployment
Create Deployment to rollout a ReplicaSet
Verify Deployment, ReplicaSet & Pods
Docker Image Location: https://hub.docker.com/repository/docker/stacksimplify/kubenginx
# Create Deployment
kubectl create deployment <Deplyment-Name> --image=<Container-Image>
kubectl create deployment my-first-deployment --image=stacksimplify/kubenginx:1.0.0 

# Verify Deployment
kubectl get deployments
kubectl get deploy 

# Describe Deployment
kubectl describe deployment <deployment-name>
kubectl describe deployment my-first-deployment

# Verify ReplicaSet
kubectl get rs

# Verify Pod
kubectl get po
Step-03: Scaling a Deployment
Scale the deployment to increase the number of replicas (pods)
# Scale Up the Deployment
kubectl scale --replicas=20 deployment/<Deployment-Name>
kubectl scale --replicas=20 deployment/my-first-deployment 

# Verify Deployment
kubectl get deploy

# Verify ReplicaSet
kubectl get rs

# Verify Pods
kubectl get po

# Scale Down the Deployment
kubectl scale --replicas=10 deployment/my-first-deployment 
kubectl get deploy
Step-04: Expose Deployment as a Service
Expose Deployment with a service (NodePort Service) to access the application externally (from internet)
# Expose Deployment as a Service
kubectl expose deployment <Deployment-Name>  --type=NodePort --port=80 --target-port=80 --name=<Service-Name-To-Be-Created>
kubectl expose deployment my-first-deployment --type=NodePort --port=80 --target-port=80 --name=my-first-deployment-service

# Get Service Info
kubectl get svc
Observation: Make a note of port which starts with 3 (Example: 80:3xxxx/TCP). Capture the port 3xxxx and use it in application URL below. 

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
Observation: Make a note of "EXTERNAL-IP" if your Kubernetes cluster is setup on AWS EKS.
Access the Application using Public IP
http://<worker-node-public-ip>:<Node-Port>







### Update Deployment
# Get Container Name from current deployment
kubectl get deployment my-first-deployment -o yaml

# Update Deployment - SHOULD WORK NOW
kubectl set image deployment/<Deployment-Name> <Container-Name>=<Container-Image> --record=true
kubectl set image deployment/my-first-deployment kubenginx=stacksimplify/kubenginx:2.0.0 --record=true
Verify Rollout Status (Deployment Status)
Observation: By default, rollout happens in a rolling update model, so no downtime.
# Verify Rollout Status 
kubectl rollout status deployment/my-first-deployment

# Verify Deployment
kubectl get deploy
Describe Deployment
Observation:
Verify the Events and understand that Kubernetes by default do "Rolling Update" for new application releases.
With that said, we will not have downtime for our application.
# Descibe Deployment
kubectl describe deployment my-first-deployment
Verify ReplicaSet
Observation: New ReplicaSet will be created for new version
# Verify ReplicaSet
kubectl get rs
Verify Pods
Observation: Pod template hash label of new replicaset should be present for PODs letting us know these pods belong to new ReplicaSet.
# List Pods
kubectl get po
Verify Rollout History of a Deployment
Observation: We have the rollout history, so we can switch back to older revisions using revision history available to us.
# Check the Rollout History of a Deployment
kubectl rollout history deployment/<Deployment-Name>
kubectl rollout history deployment/my-first-deployment  
Access the Application using Public IP
We should see Application Version:V2 whenever we access the application in browser
# Get NodePort
kubectl get svc
Observation: Make a note of port which starts with 3 (Example: 80:3xxxx/TCP). Capture the port 3xxxx and use it in application URL below. 

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
Observation: Make a note of "EXTERNAL-IP" if your Kubernetes cluster is setup on AWS EKS.

# Application URL
http://<worker-node-public-ip>:<Node-Port>
Step-02: Update the Application from V2 to V3 using "Edit Deployment" Option
Edit Deployment
# Edit Deployment
kubectl edit deployment/<Deployment-Name> --record=true
kubectl edit deployment/my-first-deployment --record=true
# Change From 2.0.0
    spec:
      containers:
      - image: stacksimplify/kubenginx:2.0.0

# Change To 3.0.0
    spec:
      containers:
      - image: stacksimplify/kubenginx:3.0.0
Verify Rollout Status
Observation: Rollout happens in a rolling update model, so no downtime.
# Verify Rollout Status 
kubectl rollout status deployment/my-first-deployment
Verify Replicasets
Observation: We should see 3 ReplicaSets now, as we have updated our application to 3rd version 3.0.0
# Verify ReplicaSet and Pods
kubectl get rs
kubectl get po
Verify Rollout History
# Check the Rollout History of a Deployment
kubectl rollout history deployment/<Deployment-Name>
kubectl rollout history deployment/my-first-deployment   
Access the Application using Public IP
We should see Application Version:V3 whenever we access the application in browser
# Get NodePort
kubectl get svc
Observation: Make a note of port which starts with 3 (Example: 80:3xxxx/TCP). Capture the port 3xxxx and use it in application URL below. 

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
Observation: Make a note of "EXTERNAL-IP" if your Kubernetes cluster is setup on AWS EKS.

# Application URL
http://<worker-node-public-ip>:<Node-Port>









# Step-00: Introduction
We can rollback a deployment in two ways.
Previous Version
Specific Version
Step-01: Rollback a Deployment to previous version
Check the Rollout History of a Deployment
# List Deployment Rollout History
kubectl rollout history deployment/<Deployment-Name>
kubectl rollout history deployment/my-first-deployment  
Verify changes in each revision
Observation: Review the "Annotations" and "Image" tags for clear understanding about changes.
# List Deployment History with revision information
kubectl rollout history deployment/my-first-deployment --revision=1
kubectl rollout history deployment/my-first-deployment --revision=2
kubectl rollout history deployment/my-first-deployment --revision=3
Rollback to previous version
Observation: If we rollback, it will go back to revision-2 and its number increases to revision-4
# Undo Deployment
kubectl rollout undo deployment/my-first-deployment

# List Deployment Rollout History
kubectl rollout history deployment/my-first-deployment  
Verify Deployment, Pods, ReplicaSets
kubectl get deploy
kubectl get rs
kubectl get po
kubectl describe deploy my-first-deployment
Access the Application using Public IP
We should see Application Version:V2 whenever we access the application in browser
# Get NodePort
kubectl get svc
Observation: Make a note of port which starts with 3 (Example: 80:3xxxx/TCP). Capture the port 3xxxx and use it in application URL below. 

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
Observation: Make a note of "EXTERNAL-IP" if your Kubernetes cluster is setup on AWS EKS.

# Application URL
http://<worker-node-public-ip>:<Node-Port>
Step-02: Rollback to specific revision
Check the Rollout History of a Deployment
# List Deployment Rollout History
kubectl rollout history deployment/<Deployment-Name>
kubectl rollout history deployment/my-first-deployment 
Rollback to specific revision
# Rollback Deployment to Specific Revision
kubectl rollout undo deployment/my-first-deployment --to-revision=3
List Deployment History
Observation: If we rollback to revision 3, it will go back to revision-3 and its number increases to revision-5 in rollout history
# List Deployment Rollout History
kubectl rollout history deployment/my-first-deployment  
Access the Application using Public IP
We should see Application Version:V3 whenever we access the application in browser
# Get NodePort
kubectl get svc
Observation: Make a note of port which starts with 3 (Example: 80:3xxxx/TCP). Capture the port 3xxxx and use it in application URL below. 

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
Observation: Make a note of "EXTERNAL-IP" if your Kubernetes cluster is setup on AWS EKS.

# Application URL
http://<worker-node-public-ip>:<Node-Port>
Step-03: Rolling Restarts of Application
Rolling restarts will kill the existing pods and recreate new pods in a rolling fashion.
# Rolling Restarts
kubectl rollout restart deployment/<Deployment-Name>
kubectl rollout restart deployment/my-first-deployment

# Get list of Pods
kubectl get po






## Step-00: Introduction
Why do we need Pausing & Resuming Deployments?
If we want to make multiple changes to our Deployment, we can pause the deployment make all changes and resume it.
We are going to update our Application Version from V3 to V4 as part of learning "Pause and Resume Deployments"
Step-01: Pausing & Resuming Deployments
Check current State of Deployment & Application
# Check the Rollout History of a Deployment
kubectl rollout history deployment/my-first-deployment  
Observation: Make a note of last version number

# Get list of ReplicaSets
kubectl get rs
Observation: Make a note of number of replicaSets present.

# Access the Application 
http://<worker-node-ip>:<Node-Port>
Observation: Make a note of application version
Pause Deployment and Two Changes
# Pause the Deployment
kubectl rollout pause deployment/<Deployment-Name>
kubectl rollout pause deployment/my-first-deployment

# Update Deployment - Application Version from V3 to V4
kubectl set image deployment/my-first-deployment kubenginx=stacksimplify/kubenginx:4.0.0 --record=true

# Check the Rollout History of a Deployment
kubectl rollout history deployment/my-first-deployment  
Observation: No new rollout should start, we should see same number of versions as we check earlier with last version number matches which we have noted earlier.

# Get list of ReplicaSets
kubectl get rs
Observation: No new replicaSet created. We should have same number of replicaSets as earlier when we took note. 

# Make one more change: set limits to our container
kubectl set resources deployment/my-first-deployment -c=kubenginx --limits=cpu=20m,memory=30Mi
Resume Deployment
# Resume the Deployment
kubectl rollout resume deployment/my-first-deployment

# Check the Rollout History of a Deployment
kubectl rollout history deployment/my-first-deployment  
Observation: You should see a new version got created

# Get list of ReplicaSets
kubectl get rs
Observation: You should see new ReplicaSet.
Access Application
# Access the Application 
http://<node1-public-ip>:<Node-Port>
Observation: You should see Application V4 version
Step-02: Clean-Up
# Delete Deployment
kubectl delete deployment my-first-deployment

# Delete Service
kubectl delete svc my-first-deployment-service

# Get all Objects from Kubernetes default namespace
kubectl get all






# SERVICES





# Step-01: Introduction to Services
Service Types
ClusterIp
NodePort
LoadBalancer
ExternalName
We are going to look in to ClusterIP and NodePort in this section with a detailed example.
LoadBalancer Type is primarily for cloud providers and it will differ cloud to cloud, so we will do it accordingly (per cloud basis)
ExternalName doesn't have Imperative commands and we need to write YAML definition for the same, so we will look in to it as and when it is required in our course.
Step-02: ClusterIP Service - Backend Application Setup
Create a deployment for Backend Application (Spring Boot REST Application)
Create a ClusterIP service for load balancing backend application.
# Create Deployment for Backend Rest App
kubectl create deployment my-backend-rest-app --image=stacksimplify/kube-helloworld:1.0.0 
kubectl get deploy

# Create ClusterIp Service for Backend Rest App
kubectl expose deployment my-backend-rest-app --port=8080 --target-port=8080 --name=my-backend-service
kubectl get svc
Observation: We don't need to specify "--type=ClusterIp" because default setting is to create ClusterIp Service. 
Important Note: If backend application port (Container Port: 8080) and Service Port (8080) are same we don't need to use --target-port=8080 but for avoiding the confusion i have added it. Same case applies to frontend application and service.

Backend HelloWorld Application Source kube-helloworld

Step-03: NodePort Service - Frontend Application Setup
We have implemented NodePort Service multiple times so far (in pods, replicasets and deployments), even then we are going to implement one more time to get a full architectural view in relation with ClusterIp service.
Create a deployment for Frontend Application (Nginx acting as Reverse Proxy)
Create a NodePort service for load balancing frontend application.
Important Note: In Nginx reverse proxy, ensure backend service name my-backend-service is updated when you are building the frontend container. We already built it and put ready for this demo (stacksimplify/kube-frontend-nginx:1.0.0)
Nginx Conf File
server {
    listen       80;
    server_name  localhost;
    location / {
    # Update your backend application Kubernetes Cluster-IP Service name  and port below      
    # proxy_pass http://<Backend-ClusterIp-Service-Name>:<Port>;      
    proxy_pass http://my-backend-service:8080;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
Docker Image Location: https://hub.docker.com/repository/docker/stacksimplify/kube-frontend-nginx
Frontend Nginx Reverse Proxy Application Source kube-frontend-nginx
# Create Deployment for Frontend Nginx Proxy
kubectl create deployment my-frontend-nginx-app --image=stacksimplify/kube-frontend-nginx:1.0.0 
kubectl get deploy

# Create ClusterIp Service for Frontend Nginx Proxy
kubectl expose deployment my-frontend-nginx-app  --type=NodePort --port=80 --target-port=80 --name=my-frontend-service
kubectl get svc

# Capture IP and Port to Access Application
kubectl get svc
kubectl get nodes -o wide
http://<node1-public-ip>:<Node-Port>/hello

# Scale backend with 10 replicas
kubectl scale --replicas=10 deployment/my-backend-rest-app

# Test again to view the backend service Load Balancing
http://<node1-public-ip>:<Node-Port>/hello


# YMAL service

## Step-02: Create Simple Pod Definition using YAML
We are going to create a very basic pod definition
02-pod-definition.yml
apiVersion: v1 # String
kind: Pod  # String
metadata: # Dictionary
  name: myapp-pod
  labels: # Dictionary 
    app: myapp         
spec:
  containers: # List
    - name: myapp
      image: stacksimplify/kubenginx:1.0.0
      ports:
        - containerPort: 80
Create Pod
# Create Pod
kubectl create -f 02-pod-definition.yml
[or]
kubectl apply -f 02-pod-definition.yml

# List Pods
kubectl get pods
Step-03: Create a NodePort Service
03-pod-nodeport-service.yml
apiVersion: v1
kind: Service
metadata:
  name: myapp-pod-nodeport-service  # Name of the Service
spec:
  type: NodePort
  selector:
  # Loadbalance traffic across Pods matching this label selector
    app: myapp
  # Accept traffic sent to port 80    
  ports: 
    - name: http
      port: 80    # Service Port
      targetPort: 80 # Container Port
      nodePort: 31231 # NodePort
Create NodePort Service for Pod
# Create Service
kubectl apply -f 03-pod-nodeport-service.yml

# List Service
kubectl get svc

# Get Public IP
kubectl get nodes -o wide

# Access Application
http://<WorkerNode-Public-IP>:<NodePort>
http://<WorkerNode-Public-IP>:31231

            POD
            =====
            apiVersion: v1 # String
            kind: Pod  # String
            metadata: # Dictionary
            name: myapp-pod
            labels: # Dictionary 
                app: myapp         
            spec:
            containers: # List
                - name: myapp
                image: stacksimplify/kubenginx:1.0.0
                ports:
                    - containerPort: 80

            NODEPORT
            =========
            apiVersion: v1
            kind: Service
            metadata:
            name: myapp-pod-nodeport-service 
            spec:
            type: NodePort 
            selector:
                app: myapp 
            ports: 
                - name: http
                port: 80 # Service Port
                targetPort: 80 # Container Port
                nodePort: 31231 # NodePort




# REPLICA set

Step-01: Create ReplicaSet Definition
replicaset-definition.yml
        apiVersion: apps/v1
        kind: ReplicaSet
        metadata:
        name: myapp2-rs
        spec:
        replicas: 3 # 3 Pods should exist at all times.
        selector:  # Pods label should be defined in ReplicaSet label selector
            matchLabels:
            app: myapp2
        template:
            metadata:
            name: myapp2-pod
            labels:
                app: myapp2 # Atleast 1 Pod label should match with ReplicaSet Label Selector
            spec:
            containers:
            - name: myapp2
                image: stacksimplify/kubenginx:2.0.0
                ports:
                - containerPort: 80
Step-02: Create ReplicaSet
Create ReplicaSet with 3 Replicas
# Create ReplicaSet
kubectl apply -f 02-replicaset-definition.yml

# List Replicasets
kubectl get rs
Delete a pod
ReplicaSet immediately creates the pod.
# List Pods
kubectl get pods

# Delete Pod
kubectl delete pod <Pod-Name>
Step-03: Create NodePort Service for ReplicaSet
apiVersion: v1
kind: Service
metadata:
  name: replicaset-nodeport-service
spec:
  type: NodePort
  selector:
    app: myapp2
  ports:
    - name: http
      port: 80
      targetPort: 80
      nodePort: 31232  
Create NodePort Service for ReplicaSet & Test
# Create NodePort Service
kubectl apply -f 03-replicaset-nodeport-servie.yml

# List NodePort Service
kubectl get svc

# Get Public IP
kubectl get nodes -o wide

# Access Application
http://<Worker-Node-Public-IP>:<NodePort>
http://<Worker-Node-Public-IP>:31232


            POD
            =====
            apiVersion: apps/v1
            kind: ReplicaSet
            metadata:
            name: myapp2-rs  
            spec:
            replicas: 3
            selector:
                matchLabels:
                app: myapp2
            template: 
                metadata: # Dictionary
                name: myapp2-pod
                labels: # Dictionary 
                    app: myapp2         
                spec:
                containers: # List
                    - name: myapp2-container
                    image: stacksimplify/kubenginx:2.0.0
                    ports:
                        - containerPort: 80
            
            nodeport
            =========
            apiVersion: v1
            kind: Service
            metadata:
            name: replicaset-nodeport-service
            spec:
            type: NodePort 
            selector: 
                app: myapp2 
            ports: 
                - name: http
                port: 80
                targetPort: 80
                nodePort: 31232
            


# DEPLOYEMENT

Step-01: Copy templates from ReplicaSet
Copy templates from ReplicaSet and change the kind: Deployment
Update Container Image version to 3.0.0
Update NodePort service nodePort: 31233
Change all names to Deployment
Change all labels and selectors to myapp3
# Create Deployment
kubectl apply -f 02-deployment-definition.yml
kubectl get deploy
kubectl get rs
kubectl get po

# Create NodePort Service
kubectl apply -f 03-deployment-nodeport-service.yml

# List Service
kubectl get svc

# Get Public IP
kubectl get nodes -o wide

# Access Application
http://<Worker-Node-Public-IP>:31233

                pod
                ====
                apiVersion: apps/v1
                kind: Deployment
                metadata:
                name: myapp3-deployment
                spec:
                replicas: 3
                selector:
                    matchLabels:
                    app: myapp3
                template: 
                    metadata: # Dictionary
                    name: myapp3-pod
                    labels: # Dictionary 
                        app: myapp3         
                    spec:
                    containers: # List
                        - name: myapp3-container
                        image: stacksimplify/kubenginx:3.0.0
                        ports:
                            - containerPort: 80
                
                apiVersion: v1
                kind: Service
                metadata:
                name: deployment-nodeport-service
                spec:
                type: NodePort 
                selector: 
                    app: myapp3
                ports: 
                    - name: http
                    port: 80
                    targetPort: 80
                    nodePort: 31233




# service

Step-01: Introduction to Services
We are going to look in to below two services in detail with a frotnend and backend example
NodePort Service
ClusterIP Service
Step-02: Create Backend Deployment & Cluster IP Service
Write the Deployment template for backend REST application.
Write the Cluster IP service template for backend REST application.
Important Notes:
Name of Cluster IP service should be name: my-backend-service because same is configured in frontend nginx reverse proxy default.conf.
Test with different name and understand the issue we face
We have also discussed about in our section 05-Services-with-kubectl
cd <Course-Repo>\kubernetes-fundamentals\10-Services-with-YAML\kube-manifests
kubectl get all
kubectl apply -f 01-backend-deployment.yml -f 02-backend-clusterip-service.yml
kubectl get all
Step-03: Create Frontend Deployment & NodePort Service
Write the Deployment template for frontend Nginx Application
Write the NodePort service template for frontend Nginx Application
cd <Course-Repo>\kubernetes-fundamentals\10-Services-with-YAML\kube-manifests
kubectl get all
kubectl apply -f 03-frontend-deployment.yml -f 04-frontend-nodeport-service.yml
kubectl get all
Access REST Application
# Get External IP of nodes using
kubectl get nodes -o wide

# Access REST Application  (Port is static 31234 configured in frontend service template)
http://<node1-public-ip>:31234/hello
Step-04: Delete & Recreate Objects using kubectl apply
Delete Objects (file by file)
kubectl delete -f 01-backend-deployment.yml -f 02-backend-clusterip-service.yml -f 03-frontend-deployment.yml -f 04-frontend-nodeport-service.yml
kubectl get all
Recreate Objects using YAML files in a folder
cd <Course-Repo>\kubernetes-fundamentals\10-Services-with-YAML
kubectl apply -f kube-manifests/
kubectl get all
Delete Objects using YAML files in folder
cd <Course-Repo>\kubernetes-fundamentals\10-Services-with-YAML
kubectl delete -f kube-manifests/
kubectl get all



            apiVersion: apps/v1
            kind: Deployment
            metadata:
            name: backend-restapp
            labels: 
                app: backend-restapp
                tier: backend
            spec:
            replicas: 3
            selector:
                matchLabels:
                app: backend-restapp
            template: 
                metadata:
                labels:
                    app: backend-restapp
                    tier: backend
                spec:
                containers:
                    - name: backend-restapp
                    image: stacksimplify/kube-helloworld:1.0.0
                    ports:
                        - containerPort: 8080
            

            apiVersion: v1
            kind: Service
            metadata:
            name: my-backend-service ## VERY VERY IMPORTANT - NGINX PROXYPASS needs this name
            labels: 
                app: backend-restapp
                tier: backend
            spec:
            #type: Cluster IP is a default service
            selector:
                app: backend-restapp
            ports: 
                - name: http
                port: 8080 # ClusterIp Service Port
                targetPort: 8080 # Container Port
            

            apiVersion: apps/v1
            kind: Deployment
            metadata:
            name: frontend-nginxapp
            labels: 
                app: frontend-nginxapp
                tier: frontend
            spec:
            replicas: 3
            selector:
                matchLabels:
                app: frontend-nginxapp
            template: 
                metadata:
                labels:
                    app: frontend-nginxapp
                    tier: frontend
                spec:
                containers:
                    - name: frontend-nginxapp
                    image: stacksimplify/kube-frontend-nginx:1.0.0
                    ports:
                        - containerPort: 80
            

            apiVersion: v1
            kind: Service
            metadata:
            name: frontend-nginxapp-nodeport-service
            labels: 
                app: frontend-nginxapp
                tier: frontend     
            spec:
            type: NodePort 
            selector:
                app: frontend-nginxapp
            ports: 
                - name: http
                port: 80
                targetPort: 80
                nodePort: 31234