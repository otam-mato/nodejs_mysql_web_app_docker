  
# A full stack primitive web app. Dockerized. NodeJS/Express - frontend, MySql - backend.

<br>
For this demo, I'll be deploying a web app from another project (built using Node.JS and Express) located at https://github.com/otammato/WebApp_NodeJS_AWS_RDS_MySql.git. The previous deployment was done on EC2 and RDS instances.
<br><br>

This time, we will launch two Docker containers on an EC2 instance. One container will host the application, while the other will host the MySQL database. 

After successful deployment, the Docker images will be pushed and stored permanently within the AWS Elastic Container Registry (ECR) for centralized management and preservation.
<br><br><br>
This simple web application interacts with a MySQL database, enabling the performing of CRUD (Create, Read, Update, Delete) operations on the data stored within the database.
<br><br>

<p align="center" >
  <img width="700" alt="Screenshot 2023-01-31 at 19 23 50" src="https://user-images.githubusercontent.com/104728608/216415601-4f8b42e4-d7f6-4e0a-9274-16a062b7591d.png">
</p>

<br><br>

<p align="center" >
  <img width="700" alt="Screenshot 2023-02-01 at 20 11 38" src="https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/ccab549a-0045-489b-9ff8-02de5ef921fa">
</p>
<br><br>


<p align="center" >
  <img width="700" alt="Screenshot 2023-01-31 at 19 23 50" src="https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/cf51b385-90d9-4a47-be53-0ca66e1b797d">
</p>

<br><br>

The app's architecture diagram:

<p align="center" >
  <img width="700" alt="Screenshot 2023-01-31 at 19 23 50" src="https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/1c930171-c2a2-4931-b2d0-ea3b0df018c2">
</p>

<br><br>

The app's files:

> 1. [For creating the database (MySQL) container](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/tree/57e06b38dcba8179fc8b0ee8757b5d94c5a3a0de/web_app_files/containers/mysql)
> 2. [For creating the app (Node.JS) container](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/tree/04dd0fa47f8d01361dc133664129347503f2d292/web_app_files/containers/node_app/codebase_partner)
> 3. [A database backup](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/blob/ac9468edb44f095392065d7f610fb62e73c78ea0/web_app_files/resources/my_sql.sql)

To download all of them clone the current repository: ```git clone https://github.com/otammato/FullStack_NodeJS_MySql_Docker.git```

<br>
<br>


Pre-requisites:
1. Launch an instance. I am using Ubuntu 22.04 AWS EC2 instance.
2. Open the port to access the app. I am using 3000.
3. Install Docker on the machine: ```sudo apt-get install docker.io```
4. Install MySQL or MariaDB on the machine: ```sudo apt-get install mariadb-client-core-10.6```

<br>
<br>


## 1. MySQL containerized server

1.1. Navigate to the ```web_app_files/containers/mysql/```

1.2. Create or download the Docker [file](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/blob/3c13fb0ed84e4a9ac806bb6261581da2b1e9ce19/web_app_files/containers/mysql/Dockerfile):

```
FROM mysql:8.0.23
COPY my_sql.sql /docker-entrypoint-initdb.d/
EXPOSE 3306
```
In this modified Dockerfile:

```my_sql.sql``` is a database backup copy which I created by ```mysqldump``` command

The COPY command copies the "my_sql.sql" file from the build context (the same directory as the Dockerfile) to the ```/docker-entrypoint-initdb.d/ ``` directory inside the container. The ```/docker-entrypoint-initdb.d/``` directory is a special directory in the MySQL Docker image, and any SQL scripts placed in this directory will be automatically executed when the container starts.
Make sure that the ```my_sql.sql``` file is located in the same directory as the Dockerfile. The SQL statements from the ```my_sql.sql``` file will be executed during the container initialization.

1.3. ```sudo docker build --tag my-sql-test . ```

1.4. ```sudo docker run --name mysql_1 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=12345678 -d my-sql-test```

1.5. ```sudo docker inspect network bridge``` or ``` docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_id>```

1.6. ```mysql -h 172.17.0.2 -P 3306 -u root -p```

1.7. When testing connection to the database is established  below commands will create a user named admin with the password 12345678, grant all privileges to that user on all databases and tables, and flush the privileges to ensure the changes take effect.
```sql
CREATE USER 'admin'@'%' IDENTIFIED BY '12345678';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%';
FLUSH PRIVILEGES;
```
