# Custom CI/CD Pipeline for Odoo in Docker/Jenkins

This repository contains the necessary files to set up a custom CI/CD pipeline for Odoo CE in Docker using Jenkins.

## Pre-requisites
- Docker

## Installation
1. Clone the repository:
```bash
git clone https://github.com/He-Is-HaZaRdOuS/odoo-jenkins
```
2. Follow the instructions in the [official documentation](https://www.jenkins.io/doc/book/installing/docker/) to build and run the Jenkins Docker container until Step 4.
3. Skip Step 4a and just build the image by following Step 4b, You can choose a different name for the image if you want.
4. Skip Step 5 and execute the following command to run the Jenkins container:
```bash
docker run --name custom-jenkins-blueocean --restart=on-failure --detach   --network jenkins --env DOCKER_HOST=tcp://docker:2376   --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1   --publish 8080:8080 --publish 8069:8069 --publish 50000:50000 --volume jenkins-data:/var/jenkins_home   --volume jenkins-docker-certs:/certs/client:ro   custom-jenkins-blueocean:2.462.1-1
```
5. You can customize the left hand side of the first two `--publish` flags to change the port number for Jenkins and Odoo respectively.
6. Follow the rest of the instructions in the official documentation to set up Jenkins.

## Usage
1. Open the browser and navigate to `http://localhost:8080`
2. Log in using the initial admin password from the Jenkins container logs.
3. Install the recommended plugins.
4. Create a new pipeline job and configure it to use the Jenkinsfile from this repository.
5. Run the pipeline job.
6. Open the browser and navigate to `http://localhost:8069`
7. Log in using admin as username and password
8. Start using Odoo
