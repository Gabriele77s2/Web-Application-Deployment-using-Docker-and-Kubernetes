# Web-Application-Deployment-using-Docker-and-Kubernetes
Create a web application using Python.
Containerize the application using Docker and deploy it on a Kubernetes cluster (Minikube).
A cloud provider for hosting is optional.

### Step 1: Create a Simple Web Application
We'll use a simple Node.js application as an example. You can choose any language or framework you are comfortable with.

**Create a directory for your project:**
```bash
mkdir simple-web-app
cd simple-web-app
```

**Initialize a new Node.js project:**
```bash
npm init -y
```

**Install Express.js:**
```bash
npm install express
```

**Create an `app.js` file with the following content:**
```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello, world!');
});

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});
```

### Step 2: Dockerize the Application
Create a `Dockerfile` in your project directory with the following content:
```dockerfile
# Use the official Node.js image as the base image
FROM node:14

# Set the working directory
WORKDIR /app

# Copy the package.json and package-lock.json files
COPY package*.json ./

# Install the dependencies
RUN npm install

# Copy the rest of the application files
COPY . .

# Expose the port the app runs on
EXPOSE 3000

# Command to run the application
CMD ["node", "app.js"]
```

**Build the Docker image:**
```bash
docker build -t simple-web-app .
```

**Run the Docker container:**
```bash
docker run -p 3000:3000 simple-web-app
```

### Step 3: Set Up Kubernetes Cluster with Minikube
**Install Minikube:** Follow the instructions [here](https://minikube.sigs.k8s.io/docs/start/).

**Start Minikube:**
```bash
minikube start
```

### Step 4: Deploy the Application on Kubernetes
**Create a Kubernetes deployment file `deployment.yaml`:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-web-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: simple-web-app
  template:
    metadata:
      labels:
        app: simple-web-app
    spec:
      containers:
      - name: simple-web-app
        image: simple-web-app:latest
        ports:
        - containerPort: 3000
```

**Create a Kubernetes service file `service.yaml`:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: simple-web-app
spec:
  type: NodePort
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 30001
  selector:
    app: simple-web-app
```

**Apply the Kubernetes configurations:**
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### Step 5: Access the Application
**Get the Minikube IP:**
```bash
minikube ip
```

**Access your application:**
Open your browser and go to `http://<Minikube_IP>:30001`.
