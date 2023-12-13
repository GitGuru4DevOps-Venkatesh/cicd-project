# Let's create a sample project that involves setting up a containerized application using Docker, deploying it to a Kubernetes cluster on AWS, and managing the deployment with Helm charts.
Certainly! Let's create a sample project that involves setting up a containerized application using Docker, deploying it to a Kubernetes cluster on AWS, and managing the deployment with Helm charts. The example application will be a simple web server.

**Project Overview:**

1. **Create a Dockerized Web Application:**

   - Create a simple web application. For this example, let's use a Node.js application.

     ```javascript
     // app.js
     const http = require('http');
     const server = http.createServer((req, res) => {
       res.writeHead(200, { 'Content-Type': 'text/plain' });
       res.end('Hello, Docker and Kubernetes!\n');
     });
     const PORT = 3000;
     server.listen(PORT, () => {
       console.log(`Server running on http://localhost:${PORT}`);
     });
     ```

   - Create a Dockerfile for the application.

     ```Dockerfile
     FROM node:14

     WORKDIR /app

     COPY package*.json ./
     RUN npm install

     COPY . .

     EXPOSE 3000

     CMD [ "node", "app.js" ]
     ```

   - Build and test the Docker image locally.

     ```bash
     docker build -t my-web-app:latest .
     docker run -p 3000:3000 my-web-app:latest
     ```

2. **AWS EC2 Instance Setup:**

   - Launch an EC2 instance on AWS with Ubuntu.
   - SSH into the instance and install Docker.

     ```bash
     sudo apt update
     sudo apt install docker.io
     ```

   - Test Docker installation.

     ```bash
     sudo docker run hello-world
     ```

3. **Set up Kubernetes on AWS with kops:**

   - Install `kubectl` and `kops` on your local machine.

     ```bash
     # Install kubectl
     sudo snap install kubectl --classic

     # Install kops
     sudo snap install kops --classic
     ```

   - Create an S3 bucket for storing Kubernetes configuration:

     ```bash
     aws s3api create-bucket --bucket my-kops-state-store --region us-east-1
     ```

   - Create a Kubernetes cluster with kops.

     ```bash
     export NAME=my-cluster.k8s.local
     export KOPS_STATE_STORE=s3://my-kops-state-store

     kops create cluster \
       --node-count=2 \
       --node-size=t2.micro \
       --zones=us-east-1a,us-east-1b \
       ${NAME}
     ```

   - Update the cluster and apply changes.

     ```bash
     kops update cluster ${NAME} --yes
     kops rolling-update cluster ${NAME} --yes
     ```

4. **Deploy the Dockerized App to Kubernetes with Helm:**

   - Install Helm on your local machine.

     ```bash
     sudo snap install helm --classic
     ```

   - Create a Helm chart for your application. You can use the default `helm create` command or create your own.

   - Update the Helm chart values to include the Docker image details.

     ```yaml
     # values.yaml
     image:
       repository: your-docker-username/my-web-app
       tag: latest
     ```

   - Install the Helm chart.

     ```bash
     helm install my-web-app ./my-web-app-chart
     ```

   - Access the deployed application.

     ```bash
     kubectl get services
     ```

     Find the external IP address and access the application in a web browser.

**Note:** Ensure that you replace placeholder values such as `your-docker-username` with your actual Docker Hub username and customize the configurations based on your specific requirements. Additionally, this is a basic example, and in a real-world scenario, you would need to consider security, scaling, and other best practices.
