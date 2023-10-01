[the page is under revision]

# Node.JS + MySQL Web App. Dockerized.

<br>

> **Note:** This is part of a series of demo projects in which I explore a Node.js application.<br>
>
> The current installment involves deploying a web app using Docker. The app built using Node.js and Express, originally presented at this [GitHub Repository](https://github.com/otam-mato/nodejs_mysql_web_app_terraform.git). The previous deployment was on EC2 and RDS instances.
<br>

## Deployment Strategy

In this demo, we will deploy two Docker containers on an EC2 instance:
1. One container will host the web application.
2. The other will host the MySQL database.

Following a successful deployment, Docker images will be permanently stored in DockerHub for centralized management and preservation.

<br>

## Technologies used
- AWS
- EC2
- Node.JS
- Express
- JavaScript
- MySQL
- Docker + DockerHub
<br>

## Application Functionality

This web application interfaces with a MySQL database, facilitating CRUD (Create, Read, Update, Delete) operations on the database records.

**<details markdown=1><summary markdown="span">Detailed app description</summary>**

## Summary

The app sets up a web server for a supplier management system. It allows viewing, adding, updating, and deleting suppliers. 

The Main Solution:

#### **Dependencies and Modules**:
   - **express**: The framework that allows us to set up and run a web server.
   - **body-parser**: A tool that lets the server read and understand data sent in requests.
   - **cors**: Ensures the server can communicate with different web addresses or domains.
   - **mustache-express**: A template engine, letting the server display dynamic web pages using the Mustache format.
   - **serve-favicon**: Provides the small icon seen on browser tabs for the website.
   - **Custom Modules**: 
     - `supplier.controller`: Handles the logic for managing suppliers like fetching, adding, or updating their details.
     - `config.js`: Keeps the server's settings for connectind to the MySQL database.

#### **Configuration**:
   - The server starts on a port taken from a setting (like an environment variable) or uses `3000` as a default.

#### **Middleware**:
   - It's equipped to understand data in JSON format or when it's URL-encoded.
   - It can chat with web pages hosted elsewhere, thanks to CORS.
   - Mustache is the chosen format for web pages, with templates stored in a folder named `views`.
   - There's a public storage (`public`) for things like images or stylesheets, accessible by anyone visiting the site.
   - The site's tiny browser tab icon is fetched using `serve-favicon`.

#### **Routes (Webpage Endpoints)**:
   - **Home**: `GET /`: Serves the home page.
   - **Supplier Operations**: 
     - `GET /suppliers/`: Fetches and displays all suppliers.
     - `GET /supplier-add`: Serves a page to add a new supplier.
     - `POST /supplier-add`: Receives data to add a new supplier.
     - `GET /supplier-update/:id`: Serves a page to update details of a supplier using its ID.
     - `POST /supplier-update`: Receives updated data of a supplier.
     - `POST /supplier-remove/:id`: Removes a supplier using its ID.

#### **Starting Up**:
   - The server comes to life, starts listening for visits, and announces its awakening with a log message.

</details>

<br>

## Implementation Steps

Follow these steps for successful implementation:

1. [**Launch MySQL Containerized Server**](https://github.com/otam-mato/nodejs_mysql_web_app_docker/blob/main/README.md#1-mysql-containerized-server)
2. [**Launch Node.js App Containerized Server**](Set up a containerized Node.js application server)
3. [**Test the App**]()
4. [**Push the Images to Docker Hub**]()

## Screenshots

<p align="center">
  <img src="https://user-images.githubusercontent.com/104728608/216415601-4f8b42e4-d7f6-4e0a-9274-16a062b7591d.png" width="700px"/>
</p>

<p align="center">
  <img src="https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/ccab549a-0045-489b-9ff8-02de5ef921fa" width="700px"/>
</p>

<p align="center">
  <img src="https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/cf51b385-90d9-4a47-be53-0ca66e1b797d" width="700px"/>
</p>

<br>

## Architecture Diagram

<p align="center">
  <img src="https://github.com/otam-mato/nodejs_mysql_web_app_terraform/assets/113034133/e4ade4f4-25f4-4481-9559-e6f9329ca459" width="700px"/>
</p>

## Application Files

For the necessary application files, refer to the following links:
1. [MySQL Container Configuration](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/tree/57e06b38dcba8179fc8b0ee8757b5d94c5a3a0de/web_app_files/containers/mysql)
2. [Node.js App Container Configuration](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/tree/04dd0fa47f8d01361dc133664129347503f2d292/web_app_files/containers/node_app/codebase_partner)
3. [Database Backup](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/blob/ac9468edb44f095392065d7f610fb62e73c78ea0/web_app_files/resources/my_sql.sql)

To acquire these files, clone the current repository using the command:
```bash
git clone https://github.com/otammato/FullStack_NodeJS_MySql_Docker.git
```

## Prerequisites

Before starting, ensure the following prerequisites are met:

1. Launch an EC2 instance (recommended OS: Ubuntu 22.04).
2. Open port 3000 to access the application.
3. Install Docker on the EC2 instance using:
   ```bash
   sudo apt-get install docker.io
   ```
4. Install MySQL or MariaDB on the EC2 instance using:
   ```bash
   sudo apt-get install mariadb-client-core-10.6
   ```

## 1. MySQL Containerized Server

### Configuration

1.1. Navigate to `web_app_files/containers/mysql/`.

1.2. Create or download the Docker [file](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/blob/3c13fb0ed84e4a9ac806bb6261581da2b1e9ce19/web_app_files/containers/mysql/Dockerfile). Name it `Dockerfile` (no extension).

   ```yaml
   FROM mysql:8.0.23
   COPY my_sql.sql /docker-entrypoint-initdb.d/
   EXPOSE 3306
   ```

   - The Dockerfile configuration utilizes the `mysql:8.0.23` image.
   - It copies the `my_sql.sql` database backup to the `/docker-entrypoint-initdb.d/` directory inside the container.
   - Port 3306 is exposed.

1.3. Build a Docker image with the tag "my-sql-test":

   ```bash
   sudo docker build --tag my-sql-test .
   ```

1.4. Run a Docker container for the MySQL server:

   ```bash
   sudo docker run --name mysql_1 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=12345678 -d my-sql-test
   ```

1.5. Discover the running MySQL container's IP:

   ```bash
   sudo docker inspect network bridge
   ```

1.6. Connect to the MySQL database server using the MySQL command-line client:

   ```bash
   mysql -h <MySQL_Container_IP> -P 3306 -u root -p
   ```

1.7. Create a user named `admin` with the password `12345678`, grant all privileges, and flush privileges:

   ```sql
   CREATE USER 'admin'@'%' IDENTIFIED BY '12345678';
   GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%';
   FLUSH PRIVILEGES;
   ```

   ![MySQL Configuration Screenshot](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/0b209554-b4a3-4061-b17a-1cbf8d071296)

   > **Note:** Best practice is to store sensitive data like passwords in environment variables or a secrets management tool.

## 2. Node.js App Containerized Server

### Configuration

2.1. Navigate to `web_app_files/containers/node_app/codebase_partner`.

2.2. Create or download the Docker [file](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/blob/0a752e41bfab83f616d35690a52de4a537821928/web_app_files/containers/node_app/codebase_partner/Dockerfile). Name it `Dockerfile` (no extension).

   ```yaml
   FROM node:11-alpine
   RUN mkdir -p /usr/src/app
   WORKDIR /usr/src/app
   COPY . .
   RUN npm install
   EXPOSE 3000
   CMD ["npm", "run", "start"]
   ```

   - The Dockerfile configuration sets up a Node.js environment using the `node:11-alpine` image.
   - It creates a working directory, copies the application code, installs dependencies with npm, exposes port 

3000, and specifies the command to start the Node.js application.

2.3. Build the Docker image:

   ```bash
   docker build --tag node_app .
   ```

2.4. Identify the MySQL container's `<container_id>`:

   ```bash
   docker ps
   ```

2.5. Discover the running MySQL container's IP:

   ```bash
   docker inspect network bridge
   ```

2.6. Run a Docker container for the Node.js application:

   ```bash
   docker run -d --name node_app_1 -p 3000:3000 -e APP_DB_HOST=<MySQL_Container_IP> node_app
   ```

## 3. Test the App

3.1. Discover the running containers' IPs:

   ```bash
   docker inspect network bridge
   ```

3.2. Use `curl` to test the application:

   ```bash
   curl <Node_App_Container_IP>:3000
   ```

   ![Curl Test Screenshot](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/35abd2e4-0dbf-438c-bf16-67defb0f489e)

3.3. Access the app from the internet using your server's public DNS or IP.

   ![App Access Screenshot](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/ccab549a-0045-489b-9ff8-02de5ef921fa)

## 4. Push the Images to Docker Hub

4.1. Log in to Docker Hub:

   ```bash
   docker login
   ```

   ![Docker Hub Login Screenshot](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/fe5f189e-b9fb-48db-a940-b005f84be7e0)

4.2. View Docker images:

   ```bash
   docker images
   ```

   ![Docker Images Screenshot](https://github.com/otammato/FullStack_NodeJS_MySql_Docker/assets/104728608/6d9f5992-c639-4173-b94c-cfed2e287782)

4.3. Tag the MySQL container image:

   ```bash
   docker tag <MySQL_Image_ID> montcarotte/fullstack_nodejs_mysql_demo:mysql_server
   ```

4.4. Tag the Node.js app container image:

   ```bash
   docker tag <Node_App_Image_ID> montcarotte/fullstack_nodejs_mysql_demo:node_app
   ```

4.5. Push the MySQL container image:

   ```bash
   docker push montcarotte/fullstack_nodejs_mysql_demo:mysql_server
   ```

4.6. Push the Node.js app container image:

   ```bash
   docker push montcarotte/fullstack_nodejs_mysql_demo:node_app
   ```

![image](https://github.com/otam-mato/nodejs_mysql_web_app_docker/assets/113034133/d5e41afd-9032-43b5-9828-c05db0635384)
