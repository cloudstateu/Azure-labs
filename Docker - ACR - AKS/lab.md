# Implementing containers in Azure lab

## Overview

This lab introduces you to containers functionality and services available in Azure for containers.

## Prerequisites
* Azure CLI - [Install the Azure CLI | Microsoft Docs](https://docs.microsoft.com/pl-pl/cli/azure/install-azure-cli)
* Docker - [Get Docker | Docker Documentation](https://docs.docker.com/get-docker/)
* Node JS - [Download | Node.js](https://nodejs.org/en/download/)
* Azure account

---

## Task 1: Create Docker Image
In this section you will learn how to create Docker Image based on NodeJS simple REST API application.

1. Create folder **ContainerApp**.
1. Create file **package.json** and insert there json:
   ```json
   {
    "name": "containerapp",
    "version": "1.0.0",
    "description": "ContainerApp demo",
    "main": "index.js",
    "scripts": {
        "start": "node index.js",
        "test": "echo\"Error: no test specified\" && exit 1"
    },
    "author": "Chmurowisko",
    "license": "ISC",
    "dependencies": {
        "express": "^4.16.3"
    }
   }
   ```

1. Create file index.js and paste there code:
    ```js
    const express = require('express'); 
    const app = express(); 
    app.get('/', (req, res) => res.send('HelloWorld!')); 
    app.listen(3000, () => console.log('Exampleapplisteningonport3000!'));
    ```

1. Open your favorite console in **ContainerApp** folder and run `npm --version` command to make sure that Node.js is installed on the machine.
1. Run command `npm install`
1. Test application running in console `npm start`
1. Open browser, type **http://localhost:3000/** and check whether app is running. 
1. Press Ctrl+C in the console to shutdown the app.
1. Create file: **dockerfile** 
   > **Note:** This file has no extension.

1. Paste to dockerfile code:
    ```dockerfile
    FROM node:carbon
    WORKDIR /usr/src/app
    COPY package*.json ./
    COPY index.js ./
    RUN npm install 
    EXPOSE 3000
    ENTRYPOINT ["npm","start"]
    ```

1. Build image with command: `docker build –t containerapp` .
1. Run image typing `docker run -p 3000:3000 containerapp`
1. Open browser and go to **http://localhost:3000** and check if you will see: `Hello Word!`

---

## Task 2: Create image repository service: ACR and push image

In this section you will learn how create Azure Container Registry and how to push image to it.

1. Open Azure Portal.
1. Click **Create Resource** on left menu.
1. Click **Containers** -> **Container Registry**.
1. On the Create container registry page provide the following configurations:
   1. Registry name: **azlabregistry[YOUR-STUDENT-NUMBER]**
   1. Resource Group: **YOUR RESOURCE GROUP**
   1. Location: **West Europe**
   1. SKU: Standard
1. Click on **Create**.
1. Go to deployed resource and on left menu click **Access keys**.
1. Enable **Admin user** and copy: user name, password and login server for further use.
1. Open console and type following command replacing parameters copied from previous step
   ```powershell
   docker login –p <password> –u <username> <login server>
   ```

1. Tag image with repository entering command with your **login server** value: 
   ```powershell
   docker tag containerapp <login server>/containerapp
   ```
   
1. Push image to repository by typing in console: 
   ```powershell
   docker push <login server>/containerapp
   ```

1. After push process is finished, go to portal to Azure Container Registry.
1. Open your ACR.
1. On left menu, in Services section click **Repositories**.
1. Check if your image is on the list.

---

## Task 3: Create Azure Container Services using Kubernetes

In this section, you will learn how to create Azure Container Service using Azure CLI and how to install Kubernetes CLI (*kubectl*).

1. Install Azure CLI if you haven't already.
   > See **Prerequisites** section at the top of this lab.

2. Open command line and type `az login`
3. Procced with instruction in the console window.
4. After login process type in the console: 
   ```powershell
   az aks create --resource-group [YOUR RESOURCE GROUP] --name azLabCluster[YOUR-STUDENT-NUMBER]  --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
   ```
   
1. After cluster is deployed, install Kubernetes CLI by typing in console: `az aks installcli`
   > **Note:** On Windows, the default installation is C:\Program Files (x86)\kubectl.exe. You may need to add this file to the Windows path.

1. Get credentials to your cluster: 
   ```powershell
   az aks get-credentials --resource-group [YOUR RESOURCE GROUP] --name azLabCluster[YOUR-STUDENT-NUMBER]
   ```

1. Verify connection using command: `kubectl get nodes`

---

## Task 4: Deploy image to Kubernetes cluster.

In this section, you will learn how to create deployment file and how to deploy it.

1. Create a secret for Azure Container Registry with a following command. You will need your credentials copied from AKS **Access keys** once again
   ```powershell
   kubectl create secret docker-registry mysecret --docker-server <image-registryhost> --docker-email <your-email> --docker-username=<username> --docker-password <password>
   ```

1. Create deployment file (`deployment.yml`) inside **ContainerApp**  folder and paste following contents:
    ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: containerapp
     labels:
       app: containerapp
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: containerapp
     template:
       metadata:
         labels:
           app: containerapp
       spec:
         containers:
         - name: containerapp
           image: <your registry name>.azurecr.io/containerapp
           ports:
           - containerPort: 3000
         imagePullSecrets:
         - name: mysecret
    ```

1. Open console in **ConatinerApp** folder and run this command:    
    ```powershell
    kubectl create –f .\deployment.yaml
    ```

1. Verify status using command:
   ```powershell
   kubectl get pods
   ```

---

## Task 5: Create service and load balancer for it

In this section, you will learn how to create services for deployed PODs.

1. Open deployment.yml file
2. Add following code to the bottom of the file
    ```yaml
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: containerappservice
    spec:
      type: LoadBalancer
      ports:
      - port: 3000
      selector:
        app: containerapp
    ```

1. Open console and type
    ```powershell
    kubectl apply –f .\deployment.yaml
    ```

1. Verify status using command:
    ```powershell
    kubectl get services
    ```

1. Open in browser ip from service details.