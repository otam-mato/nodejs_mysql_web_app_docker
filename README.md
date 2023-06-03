  
# A full stack web app. Dockerized. NodeJS/Express, MySql.

<br>
For this project, we'll be deploying a web app from another project (built using Node.JS and Express) located at https://github.com/otammato/WebApp_NodeJS_AWS_RDS_MySql.git. The previous deployment was done on EC2 and RDS instances.
<br><br>

This time, we will launch two Docker containers on an EC2 instance which operates AWS Cloud9 IDE. One container will host the application, while the other will host the MySQL database. 

After successful deployment, the Docker images will be pushed and stored permanently within the AWS Elastic Container Registry (ECR) for centralized management and preservation.
<br><br><br>
This simple web application interacts with a MySQL database, enabling the performing of CRUD (Create, Read, Update, Delete) operations on the data stored within the database.
<br><br>
<p align="center" >
  <img width="700" alt="Screenshot 2023-02-01 at 20 11 38" src="https://user-images.githubusercontent.com/104728608/216415601-4f8b42e4-d7f6-4e0a-9274-16a062b7591d.png">
</p>
<br><br>

<p align="center" >
  <img width="700" alt="Screenshot 2023-01-31 at 19 23 50" src="https://user-images.githubusercontent.com/104728608/216672050-8dea2615-03aa-426b-b3c5-75fd1460a958.png">
</p>
