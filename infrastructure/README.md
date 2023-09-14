

# Final Project
## Deploying Microservices

Setting up a Kubernetes cluster using `kubeadm`, building and pushing a Docker image to Docker Hub, creating a GitHub repository, deploying Kubernetes manifests, applying GitOps with ArgoCD, and setting up a Tekton CI/CD pipeline to automate your deployments.

## Part 1: Cluster Setup with `kubeadm`

### Step 1: Install Kubeadm, Kubelet, and Kubectl

- Install `kubeadm`, `kubelet`, and `kubectl` on your Linux machine by following the official Kubernetes documentation: [Installing kubeadm, kubelet, and kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/).

### Step 2: Initialize the Cluster

- Initialize a Kubernetes cluster using `kubeadm`:

   ```bash
   sudo kubeadm init
   ```

### Step 3: Set Up kubeconfig

- Copy the `kubeconfig` file to your local machine to configure `kubectl`:

   ```bash
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

### Step 4: Join Worker Nodes

- If you have worker nodes, follow the instructions provided by `kubeadm` to join them to the cluster.

## Part 2: Building and Pushing Docker Image

### Step 1: Build Docker Image

- Build your Docker image:

   ```bash
   docker build -t <your-image-name>:<tag> <path-to-dockerfile>
   ```

### Step 2: Log in to Docker Hub

- Log in to Docker Hub using your Docker Hub username and password:

   ```bash
   docker login
   ```

### Step 3: Push Docker Image

- Push your Docker image to Docker Hub:

   ```bash
   docker push <your-image-name>:<tag>
   ```

## Part 3: Creating a GitHub Repository

### Step 1: Sign In to GitHub

- If you're not already signed in, log in to [GitHub](https://github.com/) with your GitHub username and password.

### Step 2: Create a New Repository

- Click on the "+" icon in the top-right corner and select "New repository" to create a new GitHub repository.

### Step 3: Configure the Repository

- Configure your new repository with a name, description, visibility, and other options.

### Step 4: Clone the Repository

- Clone the GitHub repository to your local machine using `git clone`.

   ```bash
   git clone <repository-url>
   ```

## Part 4: Deploying Kubernetes Manifests

- Deploy your Kubernetes manifests (YAML files) to the cluster using `kubectl apply`.

   ```bash
   kubectl apply -f <manifest-file.yaml>
   ```

   Repeat this step for all your manifests.

## Part 5: Applying GitOps with ArgoCD

### Step 1: Install ArgoCD

- Install ArgoCD on your Kubernetes cluster by following the official [ArgoCD installation guide](https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argocd).

### Step 2: Deploy Applications with ArgoCD

1. **Create Application Manifests**:

   Create Kubernetes manifests (YAML files) for your applications. These manifests describe how your applications should run in the cluster.

2. **Create ArgoCD Application Resource**:

   Create an ArgoCD Application resource YAML file. This file specifies the source of your Git repository and the desired state of your applications.

   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: my-app
   spec:
     source:
       repoURL: <repository-url>
       targetRevision: HEAD
     destination:
       namespace: <namespace>
     project: default
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
   ```

   Replace `<repository-url>` with the URL of your GitHub repository and `<namespace>` with the Kubernetes namespace where you want to deploy your applications.

3. **Apply ArgoCD Application Resource**:

   Apply the ArgoCD Application resource to deploy your applications:

   ```bash
   kubectl apply -f argocd-application.yaml
   ```

4. **Access ArgoCD UI**:

   Access the ArgoCD web UI to monitor and manage your applications:

   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```

   Open a web browser and go to `https://localhost:8080` (you might need to

 accept a self-signed certificate).

5. **Log In to ArgoCD**:

   Log in to the ArgoCD UI using the default username `admin` and the password you obtained during the ArgoCD installation.

6. **Monitor and Sync Applications**:

   In the ArgoCD UI, you can see the status of your applications and manually trigger synchronization.

7. **Automatic Synchronization**:

   With ArgoCD, your applications will automatically synchronize with the desired state whenever changes are pushed to your GitHub repository.

## Part 6: Setting Up Tekton CI/CD Pipeline

### Step 1: Install Tekton

- Install Tekton on your Kubernetes cluster by following the official [Tekton installation guide](https://tekton.dev/docs/getting-started/).

### Step 2: Create Tekton Pipeline Resources

1. **Task**: Define the tasks in your CI/CD pipeline using Tekton Task resources. These tasks can include building, testing, and deploying your applications.

2. **Pipeline**: Create a Tekton Pipeline resource that defines the sequence of tasks in your CI/CD process.

3. **PipelineRun**: Use a Tekton PipelineRun resource to trigger the execution of your pipeline. Specify the input parameters, such as source code and image details.

### Step 3: Monitor and Trigger Pipelines

- Use the Tekton CLI or webhooks to monitor and trigger your CI/CD pipelines automatically when changes are pushed to your GitHub repository.

## Part 7: Creating Docker Hub Secret (Optional)

### Step 1: Create a Docker Hub Secret

- Create a Kubernetes Secret named `docker-hub-secret` to store your Docker Hub credentials:

   ```bash
   kubectl create secret docker-registry docker-hub-secret \
     --docker-server=https://index.docker.io/v1/ \
     --docker-username=<your-docker-username> \
     --docker-password=<your-docker-password> \
     --docker-email=<your-docker-email>
   ```

   Replace `<your-docker-username>`, `<your-docker-password>`, and `<your-docker-email>` with your Docker Hub credentials.

### Step 2: Use the Secret in Tekton Pipeline

- In your Tekton Pipeline resource, reference the `docker-hub-secret` in the `spec` section to enable pulling and pushing Docker images from/to Docker Hub.

   ```yaml
   spec:
     resources:
       secrets:
       - name: docker-hub-secret
   ```

This concludes the setup of your Kubernetes cluster, Docker image build and push, GitHub repository creation, deployment of Kubernetes manifests, GitOps application with ArgoCD, and the setup of a Tekton CI/CD pipeline.
```