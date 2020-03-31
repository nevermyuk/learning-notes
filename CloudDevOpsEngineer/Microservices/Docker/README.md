## Docker Containers

- Two main components
  - Docker Desktop
  - Docker Hub

- Many apps on one host.

- OS runtime can be packaged along with code

  

### Real World Example

- Developer shares a local project
  - Work on a flask web app.
  - Installation and config of underlying OS handled by docker container
  - Another Team Member can checkout code and user docker run to run the project
  - Eliminates configuration of machines to correctly run a software project
- Data Scientist sharing Jupyter Notebook with Researcher at another University
  - DS works with jupyter style notebooks wants to share complex data science project that has multiple dependencies on C,Foxtran,R and Python Code.
  - Package up Runtime as a Docker Container .
  - Reduces setting up of environment  and dependencies when sharing project.
- Machine Learning Engineer Load Tests a Production Machine Learning Model
  - Tasked with taking new model and deploying to production
  - Concerned about how to accurately test the accuracy of new model before committing to it
  - Model recommends product to paying customers and, if it is inaccurate, costs the company a lot of money.
  - Using Containers, possible to deploy model to a fraction of the customer. (10%)
  - If there are problems, can be quickly reverted.
  - If model performs well, quickly replace existing models.

### Difference between Containers and Virtual Machine

- Size - Containers are much smaller and run as isolated process instead of virtualized hardware.
- Speed - Container can spawn in seconds compared to VM booting and launching in minutes.
- Composability - 
  - Containers are designed to be programmatically built and defined as a source code in IaC.
  - VM are replicas of manually built systems.
  - Containers make IaC workflow possible as it is defined as files and checked into source control alongside project source code.

### Docker, Linting and CircleCI

- Install Docker

#### Makefile for Docker and CircleCI

```
setup:
    python3 -m venv ~/.container-revolution-devops

install:
    pip install --upgrade pip &&\
        pip install -r requirements.txt

test:
    #python -m pytest -vv --cov=myrepolib tests/*.py
    #python -m pytest --nbval notebook.ipynb

validate-circleci:
    # See https://circleci.com/docs/2.0/local-cli/#processing-a-config
    circleci config process .circleci/config.yml

run-circleci-local:
    # See https://circleci.com/docs/2.0/local-cli/#running-a-job
    circleci local execute

lint:
    hadolint demos/flask-sklearn/Dockerfile
    pylint --disable=R,C,W1203,W1202 demos/**/**.py

all: install lint test
```

`Dockerfile` linter - hadolint

`CircleCI` for CI. 

- Can be installed locally [Link](https://circleci.com/docs/2.0/local-cli/)

-  Allows for testing in the same environment as the SaaS offering.

  

User only needs to remember to use the same commands: 

- `make install`
-  `make lint` 
-  `make test`



### Deploying to Remote Container Registry

#### ECR

--------

- Create new Dockerfile

- Use the `FROM` directive to source Python as a base image: https://hub.docker.com/_/python
- Build container locally

- Create a new ECR repository following the instructions [here](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ECR_GetStarted.html).
- Push container to the newly created ECR Repo.



## Common Issues with Containers

####  [Writing to the Host Filesystem](https://docs.docker.com/storage/volumes/)

`docker volume` command is used to create a volume and then later it is mounted to the container.

```bash
>  /tmp docker volume create docker-data
docker-data
>  /tmp docker volume ls
DRIVER              VOLUME NAME
local               docker-data
>  /tmp docker run -d \
  --name devtest \
  --mount source=docker-data,target=/app \
  ubuntu:latest
6cef681d9d3b06788d0f461665919b3bf2d32e6c6cc62e2dbab02b05e77769f4
```

####  [Configure Logging](https://docs.docker.com/config/containers/logging/configure/) for Container

Configure by selecting the type of log driver, for example `json-file`, and whether it is blocking or non-blocking. 

```bash
>  /tmp docker run -it --log-driver json-file --log-opt mode=non-blocking ubuntu 
root@551f89012f30:/#
```

The `non-blocking` mode ensures that the application won't fail in a non-deterministic manner. Make sure to read the [Docker logging guide](https://docs.docker.com/config/containers/logging/configure/) on different logging options.

#### Map Ports to the External Host?

The Docker container has an internal set of ports that [must be exposed to the host and mapped](https://docs.docker.com/engine/reference/commandline/port/). 

`docker port ` command to see exposed ports.

```bash
$ docker port foo
7000/tcp -> 0.0.0.0:2000
9000/tcp -> 0.0.0.0:3000
```

How to map the ports? 

- Use the `-p` flag [Docker `run` flags here](https://docs.docker.com/engine/reference/commandline/run/).

```bash
docker run -p 127.0.0.1:80:9999/tcp ubuntu bash
```

#### Configuring Memory, CPU and GPU

`docker run` to accept flags for setting Memory, CPU and GPU. [Resource Constraints](https://docs.docker.com/config/containers/resource_constraints/) 

```bash
docker run -it --cpus=".25" ubuntu /bin/bash
# use at max only 25% of the CPU every second.
```

## How to build a docker Image

### Instructions

- Run `docker build --tag=name .` from the directory containing your `Dockerfile`. 
- Use `docker image ls` to make sure your new Docker image is shown. 
- Run `docker run -p 8000:5000 name`.
  -  `-p` notes port 5000 from the Docker container is exposed on port 8000 on the host computer.
- Flask app is running on port 5000, but since we exposed port 8000 on our host to it, we will actually access the running app using port 8000.
  - Access it at `http://localhost:8000` 

## Sample Dockerfile

```
FROM python:3.7.3-stretch

# Working Directory
WORKDIR /app

# Copy source code to working directory
COPY . flask_app/web.py /app/
COPY . nlib /app/

# Install packages from requirements.txt
# hadolint ignore=DL3013
RUN pip install --upgrade pip &&\
    pip install --trusted-host pypi.python.org -r requirements.txt

# Expose port 80
EXPOSE 80

# Run app.py at container launch
CMD ["python", "web.py"]
```