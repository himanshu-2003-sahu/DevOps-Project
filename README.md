# DevOps Project with ArgoCD, AWS, and Istio

## Overview
This project demonstrates the deployment and management of a containerized application using **AWS EKS (Elastic Kubernetes Service)**, **ArgoCD**, and **Istio Service Mesh**.
It provides hands-on experience with setting up a complete DevOps pipeline, managing Kubernetes clusters, and ensuring smooth application delivery using GitOps practices.

## Why This Project?
The purpose of this project is to:
1. Understand the integration of Kubernetes with AWS EKS.
2. Learn how to manage application deployments with ArgoCD.
3. Explore Istio’s features like traffic management and observability.
4. Practice GitOps, where the Git repository serves as the source of truth for deployments.

By following this guide, you’ll gain practical knowledge of setting up a scalable, observable, and secure cloud-native application environment.

---

## Prerequisites
Ensure the following tools are installed and configured on your system:

1. **AWS Account**: Ensure your account is active.
2. **Git Bash**: [Download Git Bash](https://git-scm.com/downloads)
3. **AWS CLI**: [Download AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
4. **kubectl**: [Download kubectl](https://kubernetes.io/docs/tasks/tools/)
5. **eksctl**: [Download eksctl](https://eksctl.io/installation/)
6. **Istio CLI**: [Download Istio CLI](https://github.com/istio/istio/releases/tag/1.24.2)
7. **ArgoCD**: [Install ArgoCD](https://argo-cd.readthedocs.io/en/stable/getting_started/) ([Releases](https://github.com/argoproj/argo-cd/releases))

> **Note**: For AWS CLI, kubectl, eksctl, and Istio CLI, configure your system’s environment variables by adding the folder containing the executables to the `PATH` variable.

---

## Steps to Deploy

### 1. Configure AWS CLI
Run the following command and enter your AWS credentials:
```bash
aws configure
```

### 2. Create an EKS Cluster
Execute the following command to create an EKS cluster:
```bash
eksctl create cluster --name devopsprojectcluster
```
> **Caution**: EKS is a paid service. Refer to [AWS Pricing](https://aws.amazon.com/pricing/) for details.

### 3. Install ArgoCD
- Create a namespace for ArgoCD:
  ```bash
  kubectl create namespace argocd
  ```
- Apply the ArgoCD installation manifest:
  ```bash
  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
  ```
- Verify the pods:
  ```bash
  kubectl get pods -n argocd
  ```

### 4. Access the ArgoCD Dashboard
- Port forward the ArgoCD service:
  ```bash
  kubectl port-forward svc/argocd-server -n argocd 8080:443
  ```
- Access the dashboard at `https://localhost:8080` and log in using:
  - Username: `admin`
  - Password: Obtain the password using:
    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```

### 5. Deploy the Application
1. Clone the repository:
   ```bash
   git clone https://github.com/your-repo/project.git
   cd project
   ```
2. Add and commit the YAML files for your application:
   ```bash
   git add .
   git commit -m "Initial commit"
   git push
   ```
3. In the ArgoCD dashboard, configure a new application:
   - **Name**: Your application name
   - **Source**: Your Git repository URL and file path
   - **Destination**: Kubernetes cluster and namespace (`default` by default)
   - **Policy**: Automatic synchronization

### 6. Install Istio
- Install Istio with the demo profile:
  ```bash
  istioctl install --set profile=demo
  ```
- Enable Istio injection for the default namespace:
  ```bash
  kubectl label namespace default istio-injection=enabled
  ```
- Restart all pods:
  ```bash
  kubectl delete pods --all -n default
  ```
- Deploy Istio add-ons:
  ```bash
  kubectl apply -f addons/
  ```

### 7. Test the Deployment
- Forward the application service port to access it locally:
  ```bash
  kubectl port-forward svc/productpage -n default 80:9080
  ```
- Generate test traffic:
  ```bash
  while :; do curl -s -o /dev/null -w "%{http_code}" http://<your-service-url>/productpage; done
  ```

### 8. Monitor and Manage
- Use Istio’s dashboards for monitoring:
  ```bash
  istioctl dashboard kiali
  ```
  ```bash
  istioctl dashboard grafana
  ```
- Update traffic rules, destination rules, or virtual services in your Git repository to trigger automatic updates via ArgoCD.

### 9. Security Analysis
- Use **Trivy** or **SonarQube** for scanning vulnerabilities in your Kubernetes setup and application.


## Conclusion
This project helps you practice cloud-native application deployment and management using industry-standard tools. With ArgoCD, you can maintain GitOps workflows, and Istio enhances observability, traffic control, and security of your services. This hands-on guide empowers you to build and manage scalable, resilient systems effectively.

## App-resource
- https://github.com/vimallinuxworld13/eks_istio_bookinfo_app/tree/master
