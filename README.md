  
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
Open the <public_ip>:3000 in your browser (access the web interface of the application, which is now running in a containe)
=======
Open the <puplic_ip>:3000 in your browser (access the web interface of the application, which is now running in a container)


<img width="672" alt="Screenshot 2023-02-02 at 10 46 14" src="https://user-images.githubusercontent.com/104728608/216304326-f0d44a4b-37b2-4056-b22b-e2f01d749260.png">


>>>>>>> 196d0de3325fc41b2b26a122ca80fef8965a02c9
