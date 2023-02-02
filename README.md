  
# Docker_WebApp_NodeJS_AWS_RDS_MySql

In this project we will deploy the same web app from another project (bult with Node.JS/Express) from here: https://github.com/otammato/WebApp_NodeJS_AWS_RDS_MySql.git. Where it was deployed on EC2 and RDS instances.

This time we will be using the containerization and deploy the app using Docker. We will launch both containers an the same EC2 instance running AWS IDE Cloud9. One container will host the app, another container will host the MySql Database. We will launch both containers on the same instance. In the end, we will place the Docker images in the AWS ECR (Elastic container registry) to be stored permanently there.

<br><br>
<p align="center" >
  <img width="700" alt="Screenshot 2023-02-01 at 20 11 38" src="https://user-images.githubusercontent.com/104728608/216415601-4f8b42e4-d7f6-4e0a-9274-16a062b7591d.png">
</p>
<br><br>

Launch IDE (I am using AWS Cloud9), initiate git locally, make the first push to GitHub
```
git -v
echo "# Docker_WebApp_NodeJS_AWS_RDS_MySql" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin <https://github.com/.......place here your GitHub repository...................>
git push -u origin main
```

<br><br>
make the new directory, move the app files there and create the Dockerfile
```
mkdir containers
cd containers
mkdir node_app
cd node_app
mv ~/environment/resources/codebase_partner ~/environment/containers/node_app
cd ~/environment/containers/node_app/codebase_partner
touch Dockerfile
```
<br><br>

This is a Dockerfile for a Node.js application based on the Alpine Linux image version 11. The Dockerfile performs the following steps:

- It specifies the base image as node:11-alpine.
- It creates a directory "/usr/src/app" in the image.
- It sets the current working directory to "/usr/src/app".
- It copies the contents of the current directory to "/usr/src/app" in the image.
- It runs the command "npm install" to install the dependencies of the Node.js application.
- It exposes port 3000.
- It sets the command to run when a container is created from this image as "npm run start".

```
FROM node:11-alpine
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["npm", "run", "start"]
```
<br><br>


Push to GitHub
```
git add .
git commit -m "Docker file created"
git push -u origin

# to unstage the changes
# git reset

# to unstage partially
# git restore --staged ../../../Terraform_template/terraform.tfstate
# git restore --staged ../../../Terraform_template/terraform.tfstate.1675123997.backup
# git restore --staged ../../../Terraform_template/terraform.tfstate.backup

# this is to resolve the issue with pull requests if arise
git config pull.ff true
git pull
```

<br><br>
This is a directory tree in my Cloud9 after all the above id done:
<br>
<p align="center" >
  <img width="846" alt="Screenshot 2023-02-02 at 19 07 53" src="https://user-images.githubusercontent.com/104728608/216427012-f5721567-4be6-4a0c-8cb7-ecf44bbfcd8f.png">
</p>
<br><br>
<br><br>

These commands are used to create and run a Docker container for a Node.js application:

- docker build --tag node_app . builds a Docker image using the Dockerfile in the current directory with the tag "node_app".
- docker images lists all the Docker images present on the system.
- docker run -d --name node_app_1 -p 3000:3000 node_app runs a Docker container in the background with the name "node_app_1", maps host port 3000 to container port 3000 and uses the image "node_app".
- docker container ls lists all running containers.
- curl http://localhost:3000 sends a HTTP request to the URL "http://localhost:3000" and returns the response. This can be used to test if the Node.js application is running correctly inside the container.

<br>

```
docker build --tag node_app .
docker images
docker run -d --name node_app_1 -p 3000:3000 node_app
docker container ls
curl http://localhost:3000
```

Adjust the security group of the AWS EC2 instance to allow network traffic on port 3000 from your computer.



Open the <puplic_ip>:3000 in your browser (access the web interface of the application, which is now running in a container)

That is what you have done so far:

<br><br>
<p align="center" >
  <img width="700" alt="Screenshot 2023-02-02 at 10 46 14" src="https://user-images.githubusercontent.com/104728608/216304326-f0d44a4b-37b2-4056-b22b-e2f01d749260.png">
</p>
<br><br>

- You copied the code base into a directory, which acted as your build area [a]. You also created a Dockerfile that provided instructions for how to create a Docker image. That Dockerfile specified a FROM instruction that identified a starter image to use.
- You then ran the docker build command [b]. Docker read the Dockerfile and requested the starter image from an image repository [c]. The image repository returned the starter image file [d].
- The docker build command then finished building the image according to the instructions in the Dockerfile, which resulted in the Docker image [e].
- Finally, you ran the docker run command [f] to run a Docker container [g].


```
docker ps
docker exec -ti 4e446ba88388  sh
whoami
su node
env
```
<img width="700" alt="Screenshot 2023-02-02 at 11 07 58" src="https://user-images.githubusercontent.com/104728608/216308903-d17880bb-4714-47d9-9070-267a6f02b6b8.png">

```
docker ps
docker stop node_app_1 && docker rm node_app_1

```

passing the env variable
```
docker run -d --name node_app_1 -p 3000:3000 -e APP_DB_HOST="<ip-address>" node_ap
```

using mysqldump backup the database
```
mysqldump -P 3306 -h  <mysql-host-ip-address> -u nodeapp -p --databases COFFEE > ../../my_sql.sql
```

```
cd /home/ec2-user/environment/containers
mkdir mysql
cd mysql
```

```
touch Dockerfile
cp /home/ec2-user/environment/resources/my_sql.sql .

```

```
FROM mysql:8.0.23
COPY ./my_sql.sql /
EXPOSE 3306
```

```
docker rmi -f $(docker image ls -a -q)
sudo docker image prune -f && sudo docker container prune -f
```

```
docker build --tag mysql_server .
docker images
docker run --name mysql_1 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=rootpw -d mysql_server
docker container ls
docker exec -i mysql_1 mysql -u root -p12345678 < my_sql.sql 

docker exec -i mysql_1 mysql -u root  -p12345678 -e "CREATE USER 'nodeapp' IDENTIFIED WITH mysql_native_password BY '12345678'; GRANT all privileges on *.* to 'nodeapp'@'%';" 
```

```
docker inspect network bridge

#pass the discovered ip address of mysql_server
docker run -d --name node_app_1 -p 3001:3000 -e APP_DB_HOST=172.17.0.2 node_app
docker ps
```

```
aws ecr get-login-password \
--region us-east-1 | docker login --username AWS \
--password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com
```

```
aws ecr create-repository --repository-name node-app

docker tag node_app:latest <account_id>.dkr.ecr.us-east-1.amazonaws.com/node-app:latest

docker images

docker push <account_id>.dkr.ecr.us-east-1.amazonaws.com/node-app:latest
```

```
aws ecr list-images --repository-name node-app

aws ecr batch-delete-image \
     --repository-name node-app \
     --image-ids imageTag=latest
     
aws ecr delete-repository --repository-name node-app
```
