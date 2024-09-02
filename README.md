# TechTask
# Migrating Nordlys microservices from data center to cloud with Kubernetes

This is a step-by-step instruction for migrating Nordlys microservices from a data center to a cloud infrastructure using Kubernetes. You'll prepare an application image, set up the working infrastructure, and deploy the image.

The instruction consists of the following steps:

1. [Installing Docker](#step-1-install-docker)
2. [Setting up Kubernetes cluster](#step-2-set-up-kubernetes-cluster)
3. [Creating directory structure](#step-3-create-directory-structure)
4. [Preparing the application](#step-4-prepare-the-application)
5. [Building the docker image](#step-5-build-the-docker-image)
6. [Verifying the docker image](#step-6-verify-the-docker-image)
7. [Pushing the image to a repository](#step-7-push-the-image-to-a-repository)

---

## Step 1: Install Docker

Before proceeding, ensure Docker is installed on your machine. If not, you need to install it first. See [install Docker](https://docs.docker.com/get-docker/).

## Step 2: Set up Kubernetes cluster

You need to have a Kubernetes cluster ready for deploying your application. If you’re unfamiliar with setting up a Kubernetes cluster, consider using managed Kubernetes services like Google Kubernetes Engine (GKE), Amazon EKS, or Azure Kubernetes Service (AKS). For detailed instructions on setting up a cluster, you can refer to the official [Kubernetes documentation](https://kubernetes.io/docs/home/).

Once ready, you’ll deploy the application container in this cluster. For example, assume the application (`ExampleApp`) listens on port 8080.

## Step 3: Create directory structure

Create a specific directory structure. This structure will help separate your application code from Docker-related files, making it easier to manage the project.

First, create the necessary directories by running the following commands:
```bash
mkdir quickstart_docker
mkdir quickstart_docker/application
mkdir quickstart_docker/docker
mkdir quickstart_docker/docker/application
```

Where:

* **mkdir quickstart_docker** creates the main project directory named quickstart_docker;

* **mkdir quickstart_docker/application** creates a subdirectory named **application inside the quickstart_docker** directory, where your application code will reside;

* **mkdir quickstart_docker/docker** creates another subdirectory named **docker** inside the **quickstart_docker directory**, where Docker-related files will be stored;

* **mkdir quickstart_docker/docker/application** creates a subdirectory named **application** inside the **docker** directory, where the **Dockerfile** and other Docker-related configuration files will be placed.

After creating the directories, your project should have the following structure:
```bash
quickstart_docker/       # Catalog of the entire project
├── application/         # Directory for the application code
└── docker/              # Directory for Docker-related files
    └── application/     # Subdirectory containing the Dockerfile for the application
```

This directory structure will help ensure that your Docker setup is organized and ready for deployment.
## Step 4: Prepare the application
To deploy the application, first, create a simple application and then replace it with your production-ready code. For now, create a file named **application.py** inside **/quickstart_docker/docker/application**.
Add the following content to the file:

```bash
import http.server
import socketserver

PORT = 8000

Handler = http.server.SimpleHTTPRequestHandler
httpd = socketserver.TCPServer(("", PORT), Handler)
print("serving at port", PORT)
httpd.serve_forever()
```

## Step 5: Build the Docker image
Your application needs an environment to run. Instead of building everything from scratch, use a base image from Docker Hub.

In the **/quickstart_docker/docker/application** directory, create a **Dockerfile** with the following content:
```bash
# Use the official Python 3.5 base image from Docker Hub
FROM python:3.5

# Set the working directory inside the container to /app
WORKDIR /app

# Copy the contents of the 'application' directory into the container at /app
COPY ./application /app

# Make port 8000 available to the outside world
EXPOSE 8000

# Define the command to run the application when the container starts
CMD ["python", "/app/application.py"]
```

Open your terminal and run the following command to build the Docker image:
```bash
docker build . -f docker/application/Dockerfile -t exampleapp
```
Where:

* **-f docker/application/Dockerfile** specifies the path to the **Dockerfile** relative to the build context;

* **-t exampleapp** tags the resulting Docker image with the name **exampleapp**, making it easier to reference and manage.

## Step 6: Verify the Docker image
List all Docker images to verify your image was created successfully:

```bash
docker images
```

The response will look like the following:
```bash
REPOSITORY     TAG     IMAGE ID       CREATED        SIZE
exampleapp     latest  83ioe0edc28a   2 seconds ago  154MB
python         3.5     05stv8636w3f   6 weeks ago    154MB
```

Where:

* **REPOSITORY** is the name of the image repository;

* **TAG** is the tag associated with the image (e.g., latest);

* **IMAGE ID** is a unique identifier for the image;

* **CREATED** is the time when the image was created;

* **SIZE** is the size of the image.
  
## Step 7: Push the image to a repository
Finally, push your Docker image to a container registry (e.g., Docker Hub, Google Container Registry) to make it accessible for deployment in Kubernetes.

```bash
docker tag exampleapp username/repository:tag
docker push username/repository:tag
```
Replace **username/repository:tag** with your repository details.
