# Build a Node Js Application from scratch and deploy to Production

## Prerequisites:

* Ensure you have Node.js and npm installed on your local development machine.
* Set up a Docker Hub account or another container registry (e.g., Docker Hub, AWS ECR, DOCR) to store your Docker images.
* Install Docker and Kubernetes on your local machine or use a cloud-based Kubernetes solution.
  
## Install Node.js

Install Node.js on your development machine. You can download the installer from the official Node.js website.

## Initialize Application
Create a new directory for your Node.js application and run the following command to initialize the project:
```
mkdir nodejs-app
cd nodejs-app
npm init
```
Follow the prompts to generate a package.json file.


## Install Dependencies
Install the necessary dependencies for your Node.js application. For example, if you're creating a simple Express.js application, you can run:

```
npm install express
```
## Create an Application File
Create your Node.js application file (e.g., app.js) and write your application code in it.
Example Off app.js:

```
const express = require('express'); //Import the express dependency
const app = express();              //Instantiate an express app, the main work horse of this server
const port = 5005;                  //Save the port number where your server will be listening

//Idiomatic expression in express to route and respond to a client request
app.get('/', (req, res) => {        //get requests to the root ("/") will route here
    res.sendFile('index.html', {root: __dirname});      //server responds by sending the index.html file to the client's browser
                                                        //the .sendFile method needs the absolute path to the file, see: https://expressjs.com/en/4x/api.html#res.sendFile 
});

app.listen(port, () => {            //server starts listening for any attempts from a client to connect at port: {port}
    console.log(`Now listening on port ${port}`); 
});

```
Also Create your Node.js application file `` index.html `` and write your html code in it.

## Test the Application Locally
You can test your Node.js application locally by running:

```
node app.js
```
Access your application in a web browser at `` http://localhost:5005 `` or the appropriate port.

## Dockerize Application (Create Dockerfile)
Create a Dockerfile in your project directory to package your Node.js application into a Docker image. Here's an example Dockerfile for a Node.js application:

```
# Use the official Node.js image
FROM node

# Set the working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install application dependencies
RUN npm install

# Copy application code
COPY . .

# Expose the port your app runs on
EXPOSE 5005

# Start the application
CMD ["node", "app.js"]
```

## Build and Push Image to a Docker Registry
Build your Docker image and push it to a container registry. Replace <username>/<image> with your Docker Hub username and image name:

```
docker build -t <username>/<image> .
docker push <username>/<image>
```
## Create a Kubernetes Manifest File
Create a Kubernetes manifest file (e.g., app.yml) to define how your application will run in a Kubernetes cluster. Here's an example for a Deployment and Service:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nodejs-app
  template:
    metadata:
      labels:
        app: nodejs-app
    spec:
      containers:
      - name: nodejs-app
        image: <username>/<image> # your dockerhub username and docker hub image
        ports:
        - containerPort: 5005

---
apiVersion: v1
kind: Service
metadata:
  name: nodejs-app-service
spec:
  selector:
    app: nodejs-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5005
  type: LoadBalancer
```

## Create a Kubernetes Cluster
Set up a Kubernetes cluster, either locally using tools like Minikube or in the cloud using a service like AWS EKS, Google GKE, or Azure AKS.

## Deploy the Application onto the Kubernetes Cluster
Apply the Kubernetes manifest file to deploy your application to the cluster:
```
kubectl apply -f app.yml
```
## Test Your Deployed Application
Access your application using the external IP provided by the LoadBalancer service (for cloud deployments) or use port forwarding if running locally:
```
kubectl port-forward service/nodejs-app-service 85:80
```
Access your application in a web browser at ``` http://localhost:85 ```.


