# University Student Portal

This is a web application I developed as part of my university DevOps project. It uses Flask for the backend and Redis to store the visit counter. I used Docker and Docker Compose to run the application in containers and Ansible to automate its deployment on a remote server.

## About the Project

The main purpose of this project was to get practical experience with DevOps tools and understand how they work together.

The application has a homepage, a health-check endpoint and a visit counter. Every time the `/visits` endpoint is accessed, the counter increases and the updated value is stored in Redis.

The project uses two services: Flask and Redis. Docker Compose manages both containers and handles communication between them.

## Technologies Used

- Python 3.12
- Flask 3.0.3
- Redis 7
- Docker
- Docker Compose
- Ansible
- Git

## Application Features

- Homepage displaying the portal status
- Health-check endpoint
- Visit counter connected to Redis
- Separate containers for Flask and Redis
- Persistent Redis data using a Docker volume

## Application Endpoints

| Endpoint | Description |
|---|---|
| `/` | Displays the portal welcome message |
| `/health` | Returns the application health status |
| `/visits` | Increments and returns the visit counter |

## Project Architecture

The project consists of two services:

1. **Web service:** Runs the Flask application inside a Docker container. It listens on port 5000 inside the container and is accessible through port 8080 on the host machine.
2. **Redis service:** Runs in a separate container and stores the visit counter.

Docker Compose manages both services and connects them through a shared network. The Flask application communicates with Redis using the service name `redis`.

I also configured a named Docker volume to keep Redis data persistent when the containers are restarted.

## Project Structure

```text
uni-devops-lab/
│
├── app/
│   ├── app.py
│   └── requirements.txt
│
├── ansible/
│   ├── inventory.ini
│   └── deploy.yml
│
├── Dockerfile
├── compose.yml
└── README.md


Installation and Deployment
1. Clone the Repository
git clone <your-repository-url>
cd uni-devops-lab


2. Build the Docker Image
sudo docker compose build


3. Start the Application
sudo docker compose up -d


4. Check Running Containers
sudo docker compose ps


5. Access the Application
Open the following address in your browser:

http://localhost:8080
6. Test the Endpoints
curl http://localhost:8080/
curl http://localhost:8080/health
curl http://localhost:8080/visits


Calling /visits multiple times increases the counter stored in Redis.
7. Stop the Application
sudo docker compose down


Challenges I Encountered
During the development of this project, I faced a few issues while configuring Docker, Docker Compose and the Flask application. Here are the main problems I encountered and how I fixed them.
1. Docker Daemon Was Not Running
Initially, Docker commands could not connect to the Docker daemon because the Docker service was not running.

I started the service and enabled it to launch automatically during system startup:
sudo systemctl start docker
sudo systemctl enable docker


After that, I checked the service status and ran the hello-world container to make sure Docker was working properly.
2. Docker Compose Configuration Errors
While writing the compose.yml file, I made some YAML indentation mistakes and had a duplicate mapping key. These errors prevented Docker Compose from reading the configuration correctly.

I corrected the indentation, removed the duplicate key and validated the file using:
sudo docker compose config


This helped me make sure the Compose configuration was valid.
3. Flask Container Exited with an Error
When I first started the application, the Flask container exited with a non-zero status code.

I checked the container logs:
sudo docker compose logs web


The problem turned out to be an IndentationError in app.py. Some lines inside the route functions were not indented correctly.

I fixed the indentation and rebuilt the image before starting the services again:
sudo docker compose build web
sudo docker compose up -d


4. Docker Compose Reported No Services to Build
When I initially tried to build the web service, Docker Compose reported that there were no services to build.

The reason was that I had specified an image name in compose.yml but had not added a build context.

I fixed this by adding build: . under the web service. This allowed Docker Compose to use the Dockerfile in the project directory to build the image.

After updating the configuration, the image built successfully.
Ansible Deployment
After setting up the application with Docker Compose, I used Ansible to automate its deployment on a remote application server.

I created two configuration files:
- ansible/inventory.ini contains the remote server's IP address and SSH username.
- ansible/deploy.yml contains the tasks needed to configure the server and deploy the application.
Deployment Process
The playbook automates the following steps:
1. Downloads the Docker repository configuration.
2. Installs Docker Engine, Docker Compose plugin and the required packages.
3. Enables and starts the Docker service.
4. Creates the application directory on the remote server.
5. Copies the project files to the server.
6. Builds the Docker image.
7. Starts the Flask application and Redis using Docker Compose.
8. Checks the /health endpoint to verify that the application is running.
Running Ansible
First, I installed the required Docker collection:
ansible-galaxy collection install community.docker


Then, I checked the playbook for syntax errors:
ansible-playbook -i ansible/inventory.ini ansible/deploy.yml --syntax-check


To run the deployment, I used:
ansible-playbook -i ansible/inventory.ini ansible/deploy.yml -K


The -K option allows Ansible to ask for the privilege escalation password.
Deployment Verification
After running the playbook, I checked that the application was available on the remote server through port 8080.

I also tested the health-check endpoint and Redis visit counter to make sure both services were working correctly.

To test data persistence, I checked the Redis counter, restarted the containers and checked the value again. The counter remained stored after the restart.

I also ran the playbook a second time to check its idempotency. This helped me verify whether Ansible could recognize that the server was already configured without making unnecessary changes.
What I Learned
Working on this project gave me practical experience with several DevOps tools and concepts. I learned how to:
- Build Docker images using a Dockerfile.
- Run and manage multiple containers with Docker Compose.
- Connect a Flask application to Redis.
- Use Docker volumes to preserve application data.
- Manage Docker services on a Linux server.
- Configure and deploy an application on a remote server using Ansible.
- Troubleshoot Python, YAML and container-related errors.
- Track project changes using Git.
About the Developer
Aydan Musayeva

Computer Engineering Student

I am interested in software engineering, computer graphics and simulation. Through this project, I wanted to explore DevOps practices and gain practical experience with containerization, service management and deployment automation.

Developed as part of my university DevOps project.
