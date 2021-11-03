# Lab1
![example workflow](https://github.com/myaqut/devops/actions/workflows/main.yml/badge.svg?branch=main)

**Table of Contents**

1. About Lab .
2. Getting Started :
	- Prequestes. 
	- Installation. 
3.  Add the time function to your app.
4. Unit testing. 

## About Lab:
This lab is mainly creating web application using python and deploy it on AWS instance. The application function is to show the current time and date in Moscow. You can view the live application throught the following link: [ec2-18-117-114-7.us-east-2.compute.amazonaws.com](http://ec2-18-117-114-7.us-east-2.compute.amazonaws.com "ec2-18-117-114-7.us-east-2.compute.amazonaws.com") .

## Getting Started: 
### Prequestes :
This setion is for preparing the required enviroment and libraries in order to get started.
1. Starting up an EC2 instance:
	- create an AWS account if you don't have it already.
	- Open AWS EC2 Console from https://console.aws.amazon.com/ec2/
	- Launch Intance
	- Choose AMI and find Ubuntu Server.
	- Configure the instance : 
		- On security group settings , add http on port 80 and allow all IP addresses . [![inbound security](https://miro.medium.com/max/1050/0*GfxWzIlIf9HzXzEO "inbound security")](https://miro.medium.com/max/1050/0*GfxWzIlIf9HzXzEOhttp:// "inbound security")
		- Make sure to download your pair key, and follow the following [steps](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html "steps") in order to establish ssh connection using linux.
		- You can also connect using EC2 [interface](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-methods.html "interface") .  
[Links](http://localhost/)
2. Installing dependencies :
	- Install python and flask.
`sudo apt-get install python3`
`sudo apt-get install python3-pip `
` sudo pip install flask`
	- Create Directory for your app.
`cd ~`
`mkdir ~/flaskapp`
`sudo ln -sT ~/flaskapp /var/www/html/flaskapp`
	- Verify your app is running .
	`cd ~/flaskapp `
	`echo "Hola" > index.html`
	- You should see hola displayed on your screen when you enter your instance public IP/index.
	- Add the following module in order to use it for the time function in your app.
`sudo pip install pytz`
`sudo pip install datetime`

## Installation :
1- Create flasskapp.py file inside your app folder created previously and add the following.

```python
from flask import Flask app = Flask(__name__)
@app.route('/') 
def hello_world():
   return 'Hello from Flask!' 
if __name__ == '__main__':
   app.run()
```
2- Create flaskpp.wsgi file in order to load your appplication and add the following code.

```python
#flaskapp.wsgi
import sys 
sys.path.insert(0, '/var/www/html/flaskapp')
  
from flaskapp import app as application

```
3- Enable mod_wsgi:

-  We need to replace the default html pages of appaches with your application, in order to do that we should do few changes to appache configuration file located in**  /etc/apache2/sites-enabled/000-default.conf**.

-  Add the following to the file after **DocumentRoot** line.
	
```html
WSGIDaemonProcess flaskapp threads=5
WSGIScriptAlias / /var/www/html/flaskapp/flaskapp.wsgi  
<Directory flaskapp>
     WSGIProcessGroup flaskapp
     WSGIApplicationGroup %{GLOBAL}
     Order deny,allow
     Allow from all 
</Directory>
```
4- Restart your server. 
`$ sudo service apache2 restart`

5- In case your server gave internal error please run the following in order to force wsgi to run the app using python 3, and then restart your server.
`sudo apt-get install libapache2-mod-wsgi-py3`

## Add the time function to your app
You can add the time function to your app by simply replacing your flaskapp.py file with the file already pre made in the rebo.

# Deploying Flask App Using Docker


### Table of Content

1-  Overview

2- Getting Started
- Prerequisites
- Steps


## Overview

This Repo presents the steps needed in order to deploy Flask application using Docker. Using Docker you won't need to add the dependencies on your machine or do any configuration. Everything is automated and isolated from the rest of your machine. Docker runs as a separate process on your machine.

## Getting Started

### Prerequisites : 
The only two prerequisites is to install docker engine and composer on your machine. Please follw the next steps according to your OS.
##### Docker Enginge : 
- For windows users:  click [here](https://docs.docker.com/desktop/windows/install/ "here")
- For linux users : click [here](https://docs.docker.com/engine/install/ubuntu/ "here").

##### Docker Composer :
you can find the required steps for both linux and windows on the following [link](https://docs.docker.com/compose/install/ "link"). 

##### Docker Linter:
For this repo I used vscode and installed docker extenstion as [Linter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker "Linter") for formatting.

### Getting Started : 

1- Create new folder and add your flaskapp.py file inside that folder. 
2- Create new file inside the same folder called requirements.txt in order to add the modules and dependencies required for the application.

    flask
    pytz
    datetime
3- Create docker file named Dockerfile and add the following conent. 

    # build and image starting with pythom 3.8 which is the version needed for our app
    FROM python:3.8-slim-buster
    # set the working directory
    WORKDIR /code
    #set the variable used by flask command
    ENV FLASK_APP=flaskapp.py
    ENV FLASK_RUN_HOST=0.0.0.0
    #copy and install the depndeincies in the file requirments.txt
    COPY requirements.txt requirements.txt
    RUN pip install -r requirements.txt
    #set the port for the container to be 5000
    EXPOSE 5000
    #copy the current files to the file 
    COPY . .
    #set the default command for the container to flask run 
    CMD ["flask", "run"]
    
4- Add docker-compose.yml file in order to define the running services for the container.

    version: "3.9"
    services:
      web:
        build: .
        ports:
          - "5000:5000"
      redis:
        image: "redis:alpine"

5- Build your app using compose 
` docker-compose up`

6- Open your browser and type http://localhost:5000/. You should see the time of moscow displayed on your browser screen.


# Unit Tests: 

## Test that the time is refreshing using unit testing
We need to make sure that everytime we get the time it's different than the previous one. This why the time is actually updating and refreshing each time you refresh the application. Follow the next steps in order to establish the test. In case the application doesn't refresh the time, the test is going to fail.
1. Add the file named unitest.py in your app folder.
2. Install unitest module.
`sudo apt-get install -y python-unittest2`
2. Run unitest.py using python3.
`python3 ./flaskapp/unitest.py `


# Git Actions CI: 

**Table of Contents**

1. About Lab .
2. Steps :

## About Lab:
This lab is mainly setup Git actions CI. We are going to create CI workflow using Git actions. Our workflow is automatically working on "push" event to the main branch. The workflow described below contains the following steps :
	1- run unit tests.
	2- build docker image.
	3- use build cache.
	4- push to docker hub. 
	5- add workflow status badge.

## Steps:
#### 1- Create new workflow.
- Login to your github account.
- Click actions 
- Choose create new workflow 
- Choose Jekyll template. A template is going to be created for you with the following content : 

````
name: Jekyll site CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Build the site in the jekyll/builder container
      run: |
        docker run \
        -v ${{ github.workspace }}:/srv/jekyll -v ${{ github.workspace }}/_site:/srv/jekyll/_site \
        jekyll/builder:latest /bin/bash -c "chmod -R 777 /srv/jekyll && jekyll build --future"

````
- change the workflow to CI to docker hub. make it only get triggered on push to the main branch. the file content after the previous changes is going to be the following : 

```
# This is a basic workflow to help you get started with Actions

name: CI to Docker Hub

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
   
  CI:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      
      - name: Checkout
        uses: actions/checkout@v2

```

#### 2- Run Unit Test.
- Install the required dependencies in order to run the unit test file.
- Run the unit test file using pyton3.
- Your file should look like the following after adding these steps:

```
# This is a basic workflow to help you get started with Actions

name: CI to Docker Hub

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
   
  CI:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      
      - name: Checkout
        uses: actions/checkout@v2

      
      - name: install python 3 
        uses: actions/setup-python@v1
        with:
          python-version: 3.8
          
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r ./flaskapp/requirements.txt
      - name: Run tests with pytest
        run: python3 ./flaskapp/unitest.py 

```

#### 3- Build Docker Image and Push To Dockerhub.
- Build docker image.
- Login to your docker hub account .
- Push the built image to your dockerhub account.
Your file should look liek the following :

```
# This is a basic workflow to help you get started with Actions

name: CI to Docker Hub

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
   
  CI:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      
      - name: Checkout
        uses: actions/checkout@v2

      
      - name: install python 3 
        uses: actions/setup-python@v1
        with:
          python-version: 3.8
          
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r ./flaskapp/requirements.txt
      - name: Run tests with pytest
        run: python3 ./flaskapp/unitest.py 

      - name: Login to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKER_HUB_USERNAME }}
          password: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}

      - name: Set up Docker Buildx
        id: buildx
        uses: docker/setup-buildx-action@v1

      - name: Build and push
        id: docker_build
        uses: docker/build-push-action@v2
        with:
          context: .
          file: dockerfile
          push: true
          tags: ${{ secrets.DOCKER_HUB_USERNAME }}/timeappgitworkflow:latest

      - name: Image digest
        run: echo ${{ steps.docker_build.outputs.digest }}

```
#### 4- Add your secret key github.
- Login to your dockerhub account
- Go to account settings and then security 
- Create new access token and save it
- Go to your github account
- Go to settings under your repo
- Go to secrets and add new one with the name "DOCKER_HUB_ACCESS_TOKEN"
- Add your docker hub username with the name " DOCKER_HUB_USERNAME "

#### 5- Run Your workflow.
- Do a push event to your repo and check the status of your workflow and make sure that all the steps are executed.

### 6- Add Workflow Status Badge.
- Add the following line to your repo 
```
![example workflow](https://github.com/myaqut/devops/actions/workflows/main.yml/badge.svg?branch=workflow)
```


# Jenkins CI 
The following steps are for creating CI workflow on jenkins using Multibranch Pipeline.
## Steps: 

#### 1. Pull Jenkins Docker Image
- Use the following command to pull Jenkins image from docker to your machine.
`docker pull jenkinsci/blueocean`
- Create a new directory to save jenkins password and run the docker image using the following command 
```
mkdir jenkins && cd jenkins docker run --rm --name jenkins -p 8080:8080 -p 50000:50000 -u 0 -v `pwd`:/var/jenkins_home jenkinsci/blueocean
```

#### 2. Configure Jenkins User
- Run localhost:8080 in your browser.
- Copy the password key printed on your terminal after running the docker image and enter it.
- Create a username and password for you to login with in the future.
#### 3. Create New Multi Branch Job
- Create new job and choose multi branch pipeline.
- Copy and paste your git repo url to branch resource.
- On behavior field, set to discover branch, use use filter by name and type "main".
-  
#### 4. Create Credentials For Github
- Add your email and password for your github.
- Select the credentials you just added for your git repo source.
#### 5. Scan Multiple Branch Now
- Check the logs and make sure that it reads your repo

#### 6. Create Jenkins File
- Create file in your repo with name "Jenkinsfile".
- Add the following content to the file.
```
pipeline {

environment {

DOCKERHUB_CREDENTIALS=credentials('docker-creds-id')

}

agent { label "master" }

stages{

stage('clone'){

steps{

git branch: 'main', credentialsId: 'f707ba26-5c29-4630-a9a4-32b64edd7d10', url: 'https://github.com/myaqut/devops.git'

}

}

stage('testing') {

agent { docker { image 'python:3.9.6-alpine3.14' } }

steps {

sh 'python -m pip install --upgrade pip'

sh 'pip install -r ./flaskapp/requirements.txt'

sh 'python3 ./flaskapp/unitest.py'

}

}

stage('Building our image') {

steps {

sh """

docker build -t yaqot/timeappjenkins:latest .

"""

}

}

stage('Login') {

steps {

sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'

}

}

stage('Push') {

steps {

sh 'docker push yaqot/timeappjenkins:latest'

}

}

}

}
```

- Scan your branch again and make sure that the jenkinsfile started.

#### 7. Add Dockerhub Creds
- Add your dockehub username and secret key in the credentials.
- Add dockerhub creds ID in your jenkinsfile instead of the placeholder.

#### 8. Expose Docker Service File To Jenkins.
- Run your Jenkins Using the following command : 
``` 
docker run --rm --name jenkins -p 8080:8080 -p 50000:50000 -u 0 -v `pwd`:/var/jenkins_home  -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean
```
#### 8. Run From Dockerhub
- Run your job again.
- Make sure that all stages are passed.
- Pull the image from dockerhub and run it on your machine.

