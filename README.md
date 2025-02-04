# Project-V-Containerizing-a-Java-Application-with-Docker
* Scenario:
You're managing a multi-tier application running on virtual machines (VMs), with regular deployments and continuous updates. However, this setup has some significant challenges.
* The Problem:
     * High Costs: Increased capital (CapEx) and operational expenses (OpEx).
     * Human Errors: Deployment inconsistencies due to manual processes.
     * Scalability Issues: Not compatible with a microservices architecture.
     * Resource Wastage: Inefficient use of computing power.
     * Lack of Portability: Applications don't run consistently across different environments.

* The Solution: Containerization!
  * By using Docker, we can:
    * ✅ Reduce resource consumption - Containers are lightweight and efficient.
    * ✅ Enable microservices - Perfect for modern application design.
    * ✅ Standardize deployment - Use Docker images for consistency.
    * ✅ Ensure portability - The same container image runs in all environments.
    * ✅ Improve reusability - Easily repeat deployments with minimal effort.
* Why Containerization Matters?
     - 📊 81% of organizations rely on DevOps teams to manage containerized environments
     - 📊 78% of cloud users need containerization knowledge to operate efficiently
       
* Tools Used:
  * Docker - Container runtime for building and running images.
  * Docker Compose - To manage multi-container applications.
  * Nginx - Web server for load balancing and reverse proxy.
  * Tomcat - Serves the Java-based application.
  * MySQL - Database management.
  * Memcached - Caching to speed up performance.
  * RabbitMQ - Message broker for efficient communication.

* Implementation Steps:
  * 1️⃣ Select a Base Image from DockerHub.
     * Set up Docker's apt repository.
       * Add Docker's official GPG key:
         
                 sudo apt-get update
                 sudo apt-get install ca-certificates curl
                 sudo install -m 0755 -d /etc/apt/keyrings
                 sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
                 sudo chmod a+r /etc/apt/keyrings/docker.asc

       * Add the repository to Apt sources:
         
                    " echo \
                      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
                          $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \ "
         
                 sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
                 sudo apt-get update
         
    
  * 2️⃣ Create a Dockerfile to customize the image.
    
    * Dockerfile for App Image [TOMCAT]
    
            FROM maven:3.9.9-eclipse-temurin-21-jammy AS BUILD_IMAGE 
            RUN  git clone https://github.com/hkhcoder/vprofile-project.git
            RUN cd vprofile-profile && git checkout containers && mvn install

            FROM tomcat:10-jdk21
            LABEL "Project"="Vprofile"
            LABEL "Author"="Imran"
            RUN rm -rf /usr/local/tomcat/webapps/*
            COPY --from=BUILD_IMAGE /vprofile-profile/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

            EXPOSE 8080
            CMD ["catalina.sh", "run"]
    * Dockerfile for DB Image [MySQL]
      
            FROM mysql:8.0.33
            LABEL "Project"="Vprofile"
            LABEL "Author"="Imran"
 
            ENV MYSQL_ROOT_PASSWORD="vproddpass"
            ENV MYSQL_DATABASE="accounts"

            ADD db_backuo.sql docker-entrypoint-initdb.d/db_backuo.sql
     * Dockerfile fo Web Image [NGINX]
        
            FROM nginx
            LABEL "Project"="Vprofile"
            LABEL "Author"="Imran"

            RUN rm -rf /etc/nginx/conf.d/default.conf
            COPY nginvprofile.conf /etc/nginx/conf.d/vproapp.conf
        
  * 3️⃣ Write a docker-compose.yml file to run multiple containers.

        version: '3.8'
        services:
            vprodb: 
              build:
                context: ./Docker-files/db
              image: vprocontainers/vprofiledb 
              container_name: vprodb
              ports:
                - "3306:3306"
              volumes:
                - vprodbdata:/var/lib/mysql
              environment:
                - MYSQL_ROOT_PASSWORD=vprodbpass

           vprocache01:
              image: memcahed
              container_name: vprocache01
              ports:
                - "11211:11211"

           vpromq01:
              image: rabbitmq
              ports:
                - "5672:5672"
              environment:
                - RABBITMQ_DEFAULT_USER=guess
                - RABBITMQ_DEFAULT_PASS=guess

          vproapp:
              build:
                context: ./Docker-files/app
              image: vprocontainers/vprofileapp
              container_name: vproapp
              ports:
                 - "8080:8080"
              volumes:
                - vproappdata:/usr/local/tomcat/webapps

          vproweb:
              build:
                  context: ./Docker-files/web
              image: vprocontainers/vprofileweb
              container_name: vproapp
              ports:
                - "80:80"


        volumes: 
            vprodbdata: {}
            vproappdata: {} 

  * 4️⃣ Test everything and push the images to DockerHub.
    
* By leveraging Docker and containerization, we optimize our Java application for efficiency, scalability, and seamless deployment across environments. 🚀


