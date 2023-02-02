  
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
ls
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
```
