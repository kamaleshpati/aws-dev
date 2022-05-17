docker version
docker login

docker pull stacksimplify/dockerintro-springboot-helloworld-rest-api:1.0.0-RELEASE


docker run --name app1 -p 80:8080 -d stacksimplify/dockerintro-springboot-helloworld-rest-api:1.0.0-RELEASE
http://localhost/hello

# For Mac with Apple Chips (use different application)
Step-1: Install Docker with Apple Chips binary (https://docs.docker.com/desktop/mac/install/) on your mac machine

Step-2: Run the simple Nginx Application container. 
docker run --name kube1 -p 80:80 --platform linux/amd64 -d  stacksimplify/kubenginx:1.0.0
http://localhost


        ## Sample Output
        kalyanreddy@Kalyans-Mac-mini-2 ~ % docker run --name kube1 -p 80:80 --platform linux/amd64 -d  stacksimplify/kubenginx:1.0.0
        370f238d97556813a4978572d24983d6aaf80d4300828a57f27cda3d3d8f0fec
        kalyanreddy@Kalyans-Mac-mini-2 ~ % curl http://localhost
        <!DOCTYPE html>
        <html>
        <body style="background-color:lightgoldenrodyellow;">
            <h1>Welcome to Stack Simplify</h1>
            <p>Kubernetes Fundamentals Demo</p>
            <p>Application Version: V1</p>
        </body>
        </html>%
        kalyanreddy@Kalyans-Mac-mini-2 ~ 


## list
docker ps
docker ps -a
docker ps -a -q

## Connect to Container Terminal
docker exec -it <container-name> /bin/sh
docker exec -it <container-name> <linuxcommand>

## Container Stop, Start
docker stop <container-name>
docker start  <container-name>

## Remove Container
docker stop <container-name> 
docker rm <container-name>

## Remove Image
docker images
docker rmi  <image-id>


<!-- 
Create your Docker hub account.
https://hub.docker.com/
Important Note: In the below listed commands wherever you see stacksimplify you can replace with your docker hub account id. 
-->

## Step-1: Run the base Nginx container
Access the URL http://localhost
docker run --name mynginxdefault -p 80:80 -d nginx
docker ps
docker stop mynginxdefault
## Step-2: Create Dockerfile and copy our customized index.html
Dockerfile
FROM nginx
COPY index.html /usr/share/nginx/html
Step-3: Build Docker Image & run it
docker build -t stacksimplify/mynginx_image1:v1 .
docker run --name mynginx1 -p 80:80 -d stacksimplify/mynginx_image1:v1

Replace your docker hub account Id
docker build -t <your-docker-hub-id>/mynginx_image1:v1 .
docker run --name mynginx1 -p 80:80 -d <your-docker-hub-id>/mynginx_image1:v1

## Step-4: Tag & push the Docker image to docker hub
docker images
docker tag stacksimplify/mynginx_image1:v1 stacksimplify/mynginx_image1:v1-release
docker push stacksimplify/mynginx_image1:v1-release

Replace your docker hub account Id
docker tag <your-docker-hub-id>/mynginx_image1:v1 <your-docker-hub-id>/mynginx_image1:v1-release
docker push <your-docker-hub-id>/mynginx_image1:v1-release






Commands	                                                                    Description
docker ps	                                                               List all running containers
docker ps -a	                                                           List all containers stopped, running
docker stop container-id	                                               Stop the container which is running
docker start container-id	                                               Start the container which is stopped
docker restart container-id	                                               Restart the container which is running
docker port container-id	                                               List port mappings of a specific container
docker rm container-id or name	                                           Remove the stopped container
docker rm -f container-id or name	                                       Remove the running container forcefully
docker pull image-info	                                                   Pull the image from docker hub repository
docker pull stacksimplify/springboot-helloworld-rest-api:2.0.0-RELEASE	   Pull the image from docker hub repository
docker exec -it container-name /bin/sh  	                            Connect to linux container and execute commands in container
docker rmi image-id	                                                       Remove the docker image
docker logout	                                                           Logout from docker hub
docker login -u username -p password	                                   Login to docker hub
docker stats	                                                        Display a live stream of container resource usage statistics
docker top container-id or name	                                           Display the running processes of a container
docker version	                                                           Show the Docker version information