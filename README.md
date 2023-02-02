  
# Docker_WebApp_NodeJS_AWS_RDS_MySql

```
git -v
echo "# Docker_WebApp_NodeJS_AWS_RDS_MySql" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/otammato/Docker_WebApp_NodeJS_AWS_RDS_MySql.git
git push -u origin main


mkdir containers
cd containers
mkdir node_app
cd node_app
mv ~/environment/resources/codebase_partner ~/environment/containers/node_app
cd ~/environment/containers/node_app/codebase_partner
touch Dockerfile

git add .
git commit -m "Docker file created"
git push -u origin

git restore --staged ../../../Terraform_template/terraform.tfstate
git restore --staged ../../../Terraform_template/terraform.tfstate.1675123997.backup
git restore --staged ../../../Terraform_template/terraform.tfstate.backup

git config pull.ff true
git pull
```

```
docker build --tag node_app .
docker images
docker run -d --name node_app_1 -p 3000:3000 node_app
docker container ls
curl http://localhost:3000
```

Adjust the security group of the AWS EC2 instance to allow network traffic on port 3000 from your computer.

<<<<<<< HEAD
=======


>>>>>>> b9945eb512fd2dedeaf908a8af58e5e1b557655c
Open the <puplic_ip>:3000 in your browser (access the web interface of the application, which is now running in a container)


<img width="700" alt="Screenshot 2023-02-02 at 10 46 14" src="https://user-images.githubusercontent.com/104728608/216304326-f0d44a4b-37b2-4056-b22b-e2f01d749260.png">

<img width="700" alt="Screenshot 2023-02-02 at 11 07 58" src="https://user-images.githubusercontent.com/104728608/216308903-d17880bb-4714-47d9-9070-267a6f02b6b8.png">

- You copied the code base into a directory, which acted as your build area [a]. You also created a Dockerfile that provided instructions for how to create a Docker image. That Dockerfile specified a FROM instruction that identified a starter image to use.
- You then ran the docker build command [b]. Docker read the Dockerfile and requested the starter image from an image repository [c]. The image repository returned the starter image file [d].
- The docker build command then finished building the image according to the instructions in the Dockerfile, which resulted in the Docker image [e].
- Finally, you ran the docker run command [f] to run a Docker container [g].

<<<<<<< HEAD

```
docker ps
docker exec -ti 4e446ba88388  sh
whoami
su node
env
```

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
=======
>>>>>>> b9945eb512fd2dedeaf908a8af58e5e1b557655c
