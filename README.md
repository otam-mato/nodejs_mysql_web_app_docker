  
# Docker web app (NodeJS/Express) + Docker MySql server

<br>
For this project, we'll be deploying a web app from another project (built using Node.JS and Express) located at https://github.com/otammato/WebApp_NodeJS_AWS_RDS_MySql.git. The previous deployment was done on EC2 and RDS instances.
<br>
We will initiate the launch of two containers on an EC2 instance that operates AWS Cloud9 IDE. One container will host the application, while the other will host the MySQL database. After successful deployment, the Docker images will be pushed and stored permanently within the AWS Elastic Container Registry (ECR) for centralized management and preservation.
<br><br><br>
This simple web application utilizes a web interface to interact with a MySQL database, enabling the performance of CRUD (Create, Read, Update, Delete) operations on the data stored within the database.
<br><br>
<p align="center" >
  <img width="700" alt="Screenshot 2023-02-01 at 20 11 38" src="https://user-images.githubusercontent.com/104728608/216415601-4f8b42e4-d7f6-4e0a-9274-16a062b7591d.png">
</p>
<br><br>

Launch your IDE (I am using AWS Cloud9), download the archive with project files and extract them in the home/ec2-user/environment
```
wget https://github.com/otammato/Docker_WebApp_NodeJS_AWS_RDS_MySql/blob/main/Docker_WebApp_NodeJS_AWS_RDS_MySql.tar.gz

tar -xzvf Docker_WebApp_NodeJS_AWS_RDS_MySql.tar.gz
```
<br><br>


Initiate git locally, set up your remote GitHub repository and make the first push to GitHub:
```
git -v
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin <https://github.com/.......place here your GitHub repository...................>

# To avoid the need to input username and password for each push, set the caching of username and password to timeout after 1 hour (setting is in seconds)
$ git config --global credential.helper 'cache --timeout=3600' 

git push -u origin main
```

<br><br>
Make the new directory for containers, move the app files there and create the first Dockerfile for your app.
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

Next, write the Dockerfile for a Node.js application based on the Alpine Linux image version 11. The Dockerfile performs the following steps:

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


Push your project to GitHub
```
git add .
git commit -m "Docker file created"
git push -u origin

# to unstage the changes
# git reset

# to unstage partially and add them to .gitignore later
# git restore --staged ../../../Terraform_template/terraform.tfstate
# git restore --staged ../../../Terraform_template/terraform.tfstate.1675123997.backup
# git restore --staged ../../../Terraform_template/terraform.tfstate.backup

# this is to resolve the issue with pull requests if arise
git config pull.ff true
git pull
```

<br><br>

This is my Cloud9 directory tree after all the above is done:
<br>
<p align="center" >
  <img width="846" alt="Screenshot 2023-02-02 at 19 07 53" src="https://user-images.githubusercontent.com/104728608/216427012-f5721567-4be6-4a0c-8cb7-ecf44bbfcd8f.png">
</p>
<br><br>
<br><br>

Run these commands to build and run the Docker container for a Node.js application:

- "docker build --tag node_app ." builds a Docker image using the Dockerfile in the current directory with the tag "node_app".
- "docker images" lists all the Docker images present on the system.
- "docker run -d --name node_app_1 -p 3000:3000 node_app" runs a Docker container in the background with the name "node_app_1", maps host port 3000 to container port 3000 and uses the image "node_app".
- "docker container ls" lists all running containers.
- "curl http://localhost:3000" sends a HTTP request to the URL "http://localhost:3000" and returns the response. This can be used to test if the Node.js application is running correctly inside the container.


```
docker build --tag node_app .
docker images
docker run -d --name node_app_1 -p 3000:3000 node_app
docker container ls
curl http://localhost:3000
```
<br><br>

Adjust the security group of the AWS EC2 instance (which hosts both Cloud9 and containers) to allow network traffic on port 3000 from your computer:
<br>
<p align="center" >
  <img width="657" alt="Screenshot 2023-02-02 at 21 52 29" src="https://user-images.githubusercontent.com/104728608/216458138-8c5325cf-87a9-4625-ab49-f5c4dc324daa.png">
</p>
<br><br>
<br><br>
<br><br>

Paste the <puplic_ip>:3000 in your browser (access the web interface of the application, which is now running in a container)
<br>
<p align="center" >
  <img width="700" alt="Screenshot 2023-02-01 at 20 11 38" src="https://user-images.githubusercontent.com/104728608/216415601-4f8b42e4-d7f6-4e0a-9274-16a062b7591d.png">
</p>
<br><br>
<br><br>
<br><br>

That is what we have done so far:
<br>
<p align="center" >
  <img width="700" alt="Screenshot 2023-02-02 at 10 46 14" src="https://user-images.githubusercontent.com/104728608/216304326-f0d44a4b-37b2-4056-b22b-e2f01d749260.png">
</p>
<br><br>

- We copied the code base into a directory, which acted as our build area [a]. We also created a Dockerfile that provided instructions for how to create a Docker image. That Dockerfile specified a FROM instruction that identified a starter image to use.
- We then ran the docker build command [b]. Docker read the Dockerfile and requested the starter image from an image repository [c]. The image repository returned the starter image file [d].
- The docker build command then finished building the image according to the instructions in the Dockerfile, which resulted in the Docker image [e].
- Finally, we ran the docker run command [f] to run a Docker container [g].
<p align="center" >  
  <img width="700" alt="Screenshot 2023-02-02 at 11 07 58" src="https://user-images.githubusercontent.com/104728608/216308903-d17880bb-4714-47d9-9070-267a6f02b6b8.png">
</p>
<br><br>


These commands provide access to the interior of the container's shell and display the existing environment variables. You can either set the variables within the container or pass them through CLI commands later. These variables are required for the JavaScript script to connect to the database. The script will utilize these variables when establishing a connection to the MySQL database. 
```
docker ps
docker exec -ti 4e446ba88388  sh  #replace 4e446ba88388 with your docker image id
whoami
su node
env
```
Here are the variables:
```
APP_DB_HOST=<paste here the output endpoint of the created RDS instance> 
APP_DB_USER=admin 
APP_DB_PASSWORD="<your password>" 
APP_DB_NAME=COFFEE
```


<br><br>

An alternative approach to setting environment variables is to pass them through the command-line interface (CLI) using a Docker command. This can be done by using the "-e" option in the Docker run command, followed by the environment variable and its value, like this:
```
docker run -d --name node_app_1 -p 3000:3000 -e APP_DB_HOST="<ip-address>" node_ap
```
<br><br>


Execute this command to export the database named "COFFEE" from a MySQL server running on the host with IP address "mysql-host-ip-address" on port 3306. The command is executed using the "mysqldump" tool and the output is saved to the file "my_sql.sql" in a parent directory of the current directory. The user connecting to the MySQL server is "nodeapp" and the password is prompted for during the execution of the command.
```
mysqldump -P 3306 -h  <mysql-host-ip-address> -u nodeapp -p --databases COFFEE > ../../my_sql.sql
```

<br><br>
Navigate to the directory "containers" and create a mysql folder
```
cd /home/ec2-user/environment/containers
mkdir mysql
cd mysql
```
<br><br>

Create a second Dockerfile for MySQL and copy a my_sql.sql file to the current directory
```
touch Dockerfile
cp /home/ec2-user/environment/resources/my_sql.sql .
vi Dockerfile
```

<br><br>
Insert these instructions in a Dockerfile, which will be used to build an image of a containerized application:

- "FROM mysql:8.0.23" specifies that the base image for the container is the official MySQL image version 8.0.23.

- "COPY ./my_sql.sql /" copies a file named my_sql.sql from the local file system to the root directory / inside the container.

- "EXPOSE 3306" informs Docker that the container listens on the specified network port at runtime. In this case, the MySQL service in the container will listen on port 3306.
```
FROM mysql:8.0.23
COPY ./my_sql.sql /
EXPOSE 3306
```

<br><br>

Execute these Docker commands to perform the following actions:
- Build a Docker image named "mysql_server" from the current directory using the "docker build" command.
- List all Docker images on the system using the "docker images" command.
- Run a Docker container named "mysql_1" from the "mysql_server" image, mapping port 3306 in the container to port 3306 on the host, setting the MySQL root password as 12345678, and running the container in the background using the "docker run" command.
- List all running Docker containers using the "docker container ls" command.
- Execute a SQL script named "my_sql.sql" into the running "mysql_1" container as the MySQL root user with the password 12345678 using the "docker exec" command.
- Execute an SQL command to create a new MySQL user named "nodeapp" with password 12345678 and grant all privileges to the user on all databases using the "docker exec" command.
```
docker build --tag mysql_server .
docker images
docker run --name mysql_1 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=12345678 -d mysql_server
docker container ls
docker exec -i mysql_1 mysql -u root -p12345678 < my_sql.sql 

docker exec -i mysql_1 mysql -u root  -p12345678 -e "CREATE USER 'nodeapp' IDENTIFIED WITH mysql_native_password BY '12345678'; GRANT all privileges on *.* to 'nodeapp'@'%';" 
```

<br><br>
Run these commands:
- to display detailed information about the default "bridge" network in Docker.
- to run a Docker container in the background (detached mode) seting an environment variable APP_DB_HOST to 172.17.0.2 (discovered by the previous comand):
- to list all the running Docker containers on your system.
```
docker inspect network bridge

#pass the discovered ip address of mysql_server
docker run -d --name node_app_1 -p 3000:3000 -e APP_DB_HOST=172.17.0.2 node_app
docker ps
```
<br><br>

Check the functionality of the app:
- load the ip address in your web browser
- click at "suppliers link" to test the interaction with MySQL Database
<br>
<p align="center" >
  <img width="700" alt="Screenshot 2023-01-31 at 19 23 50" src="https://user-images.githubusercontent.com/104728608/216672050-8dea2615-03aa-426b-b3c5-75fd1460a958.png">
</p>
<br><br>

Finally, we push the app images to AWS ECR repository.
This is AWS CLI command connects you to ECR repository. After completion you get a message with the result of login:
```
aws ecr get-login-password \
--region us-east-1 | docker login --username AWS \
--password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com
```

<br><br>
This series of commands creates an Amazon Elastic Container Registry (ECR) repository named "node_app", tags two Docker images, "node_app" and "mysql_server", with custom tags, and pushes the images to the ECR repository.

- The command "aws ecr create-repository --repository-name node_app" creates an ECR repository with the name "node_app".

- The command "docker tag node_app:latest <account_id>.dkr.ecr.us-east-1.amazonaws.com/node_app:node_app" creates a new tag for the Docker image "node_app" with the name "node_app" and associates it with the ECR repository "node_app" in the us-east-1 region.

- The command "docker tag mysql_server:latest <account_id>.dkr.ecr.us-east-1.amazonaws.com/node_app:mysql_server" creates a new tag for the Docker image "mysql_server" with the name "mysql_server" and associates it with the same ECR repository "node_app" in the us-east-1 region.

- The command "docker images" lists all the available Docker images on your system.

- The command "docker push 810453367775.dkr.ecr.us-east-1.amazonaws.com/node_app:node_app" pushes the "node_app" Docker image with the tag "node_app" to the "node_app" repository in AWS ECR.

- The command "docker push 810453367775.dkr.ecr.us-east-1.amazonaws.com/node_app:mysql_server" pushes the "mysql_server" Docker image with the tag "mysql_server" to the same "node_app" repository in AWS ECR.
```
aws ecr create-repository --repository-name node_app

docker tag node_app:latest <account_id>.dkr.ecr.us-east-1.amazonaws.com/node_app:node_app

docker tag mysql_server:latest <account_id>.dkr.ecr.us-east-1.amazonaws.com/node_app:mysql_server

docker images

docker push 810453367775.dkr.ecr.us-east-1.amazonaws.com/node_app:node_app

docker push 810453367775.dkr.ecr.us-east-1.amazonaws.com/node_app:mysql_server
```

<br><br>
- This series of commands performs the following actions on an Amazon Elastic Container Registry (ECR) repository named "node_app":

- The command "aws ecr list-images --repository-name node_app" lists all the images in the "node_app" repository in AWS ECR.

- The command "aws ecr batch-delete-image \ --repository-name node_app \ --image-ids imageTag=latest" deletes all images in the "node_app" repository with the tag "latest".

- The command "aws ecr delete-repository --repository-name node_app" deletes the entire "node_app" repository from AWS ECR.
```
aws ecr list-images --repository-name node_app

aws ecr batch-delete-image \
     --repository-name node_app \
     --image-ids imageTag=latest
     
aws ecr delete-repository --repository-name node_app
```

<br><br>
This commands will remove all unused images and containers that are present locally:
```
docker rmi -f $(docker image ls -a -q)
sudo docker image prune -f && sudo docker container prune -f
```
