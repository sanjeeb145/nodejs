1.# nodejs running locally
// create a new directory
mkdir sample-nodejs-app

// change to new directory
cd sample-nodejs-app

// Initialize npm
npm init -y

// install express
npm install express

// create an server.js file
touch server.js

2#. Creating a Dockerfile

We are going to start dockerizing the app by creating a single file called a Dockerfile in the base of our project directory.

The Dockerfile is the blueprint from which our images are built. And then images turn into containers, in which we run our apps.

Every Dockerfile starts with a base image as its foundation. There are two ways to approach creating your Dockerfile:

    Use a plain OS base image (For example, Ubuntu OS, Debian, CentOS etc.) and install an application environment in it such as Node.js OR
    Use an environment-ready base image to get an OS image with an application environment already installed.

We will proceed with the second approach. We can use the official Node.js image hosted on Dockerhub which is based on Alpine Linux.

Write this in the Dockerfile:

FROM node:8-alpine
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY . .
RUN npm install
EXPOSE 3000
CMD [ "node", "server.js" ]

Let’s walk through this line by line to see what is happening here, and why.

FROM node:8-alpine

Here, we are building our Docker image using the official Node.js image from Dockerhub (a repository for base images).

    Start our Dockerfile with a FROM statement. This is where you specify your base image.
    The RUN statement will allow us to execute a command for anything you want to do. We created a subdirectory /usr/src/app that will hold our application code within the docker image.
    WORKDIR instruction establishes the subdirectory we created as the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile. /usr/src/app is our working directory.
    COPY lets us copy files from a source to a destination. We copied the contents of our node application code ( server.js and package.json) from our current directory to the working directory in our docker image.
    The EXPOSE instruction informs Docker that the container listens on the specified network ports at runtime. We specified port 3000.
    Last but not least, theCMD statement specifies the command to start our application. This tells Docker how to run your application. Here we use node server.js which is typically how files are run in Node.js.

With this completed file, we are now ready to build a new Docker image.
6. Building a docker image

Make sure you have Docker up and running. Now that we have defined our Dockerfile, let’s build the image with a title using -t:

docker build -t sample-nodejs-app .

This will output hashes, and alphanumeric strings that identify containers and images saying “Successfully built” on the last line:

Sending build context to Docker daemon  1.966MB
Step 1/7 : FROM node:6-alpine
 ---> 998971a692ca
Step 2/7 : RUN mkdir -p /usr/src/app
 ---> Using cache
 ---> f1aa1c112188
Step 3/7 : WORKDIR /usr/src/app
 ---> Using cache
 ---> b4421b83357b
Step 4/7 : COPY . .
 ---> 836112e1d526
Step 5/7 : RUN npm install
 ---> Running in 1c6b36b5381c
npm WARN sample-nodejs-app@1.0.0 No description
npm WARN sample-nodejs-app@1.0.0 No repository field.
Removing intermediate container 1c6b36b5381c
 ---> 93999e6c807f
Step 6/7 : EXPOSE 3000
 ---> Running in 7419020927f1
Removing intermediate container 7419020927f1
 ---> ed4ac8a31f83
Step 7/7 : CMD [ "node", "server.js" ]
 ---> Running in c77d34f4c873
Removing intermediate container c77d34f4c873
 ---> eaf97859f909
Successfully built eaf97859f909

// dont expect the same values from your terminal.

7. Running a Docker Container

We’ve built the docker image. To see previously created images, run:

docker images

You should see the image we just created as the most recent based on time:
1*sQv-4uSIWP645Pp6MQu7Nw

Copy the image Id. To run the container, we write on the terminal:

docker run -p 80:3000 {image-id}

// fill with your image-id

By default, Docker containers can make connections to the outside world, but the outside world cannot connect to containers. -p publishes all exposed ports to the host interfaces. Here we publish the app to port 80:3000. Because we are running Docker locally, go to http://localhost to view.
