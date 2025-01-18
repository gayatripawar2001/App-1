# Deploy a web application in a cloud-based Kubernetes solution

This repository provides all the resources required to deploy a web application in a cloud-based Kubernetes solution with monitoring and logging. Below are the prerequisites and detailed steps to set up and deploy the solution.

![image](https://github.com/user-attachments/assets/dd305d3a-680d-4205-b55e-4a2ce69e1f8f)

## Prerequisites
Before you start, ensure the following tools are installed and configured on your system:

1. **AWS CLI**
   - Install AWS CLI: [AWS CLI Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
   - Configure AWS CLI:
     
    ```
    aws configure
    ```
    Provide your AWS access key, secret key, region, and output format.
3. **Terraform**
   - Install Terraform: [Terraform Installation Guide](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
4. **kubectl**
   - Install kubectl: [kubectl Installation Guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
   - Ensure it matches your Kubernetes version.
   - Verify installation:
     
   ```
   kubectl version --client
   ```
6. **Helm (Optional but recommended)**
   - Install Helm: [Helm Installation Guide](https://helm.sh/docs/intro/install/)
   - Verify installation:
     
  ```
  helm version
  ```
5. **Docker**
   - Install Docker: [Docker Installation Guide](https://docs.docker.com/get-docker/)
   - Verify installation:
     
  ```
  docker --version
  ```
6. **Git**
   - Install Git: [Git Installation Guide](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
   - Verify installation:
     
  ```
  git --version
  ```

# Step-by-Step Guide
**1. Clone the Repository**
    - Clone this repository to your local system: 

``` 
 git clone https://github.com/gayatripawar2001/App.git
```

```
 cd App
```

**2. Provision the Cloud Infrastructure**
    - Navigate to the Terraform directory:
    
```
cd Terraform
```

   - Initialize Terraform:
     
```
terraform init
```

   - Preview the resources to be created:
     
```
terraform plan -var-file="variables.tfvars"
```

   - Apply the configuration to create resources:
     
```
terraform apply -var-file=variables.tfvars --auto-approve"
```

**3. Build and Push the Docker Image**
   - Navigate to the webapp directory:
     
```
cd ../webapp
```

   - Build the Docker image:
     
```
docker build -t guser1/web-app:v2 .
```

   - Push the Docker image to Docker Hub:
     
```
 docker push guser1/web-app:v2
```

**4. Configure Kubernetes**
   - Update your kubeconfig to connect to the EKS cluster:
     
```
 aws eks --region us-east-1 update-kubeconfig --name EKS-Cluster
```

   - Verify the connection:
     
```
 kubectl get nodes
```

**5. Deploy the Application**
   - Navigate to the Kubernetesfiles directory:
     
```
cd ../Kubernetesfiles
```

   - Deploy the application:
     
```
kubectl apply -f deployment.yaml
```

   - Expose the application via a service:
     
```
kubectl apply -f service.yaml
```

   - Access the application Go to loadbalancer copy the DNS name and paste it into your browser
     

**6. Set Up Monitoring:**
   - using helm chart download the kube-prometheus-stack
     
     ```
     helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
     ```

     ```
     helm repo update
     ```
     
    ```helm install kube-prometheus-stack \
       --create-namespace \
       --namespace kube-prometheus-stack \
        prometheus-community/kube-prometheus-stack```

   - Now check the pods running:
     
     ```
     kubectl -n kube-prometheus-stack get pods
     ```
     
   - Port forwarding
     
     ```
     kubectl port-forward -n kube-prometheus-stack svc/kube-prometheus-stack-prometheus 9090:9090
     ```

   - Access The Prometheus dashboard:

     ```
     http://localhost:9090
     ```

   - Setup Grafana also

     ```
     kubectl port-forward -n kube-prometheus-stack svc/kube-prometheus-stack-grafana 8080:80
     ```

   - Access Grafana Page:

     ```http://localhost:8080```
     
     You’ll see the Grafana login page. The default user account is admin with a password of 'prom-operator'.
     
