DevOps Project with ArgoCD, AWS, and Istio
Overview
This project demonstrates the deployment and management of a containerized application using AWS EKS (Elastic Kubernetes Service), ArgoCD, and Istio Service Mesh. It offers hands-on experience in setting up a full DevOps pipeline, managing Kubernetes clusters, and ensuring efficient application delivery with GitOps methodologies.

Purpose of This Project
The goal of this project is to:

Learn how Kubernetes integrates with AWS EKS.
Understand the management of application deployments using ArgoCD.
Explore Istio’s capabilities in traffic management and observability.
Implement GitOps, where the Git repository acts as the single source of truth for application deployments.
By following this guide, you’ll gain practical experience in creating a scalable, observable, and secure cloud-native application environment.

Prerequisites
Make sure the following tools are installed and configured on your system:

AWS Account: Ensure your AWS account is set up.
Git Bash: Download Git Bash
AWS CLI: Download AWS CLI
kubectl: Download kubectl
eksctl: Download eksctl
Istio CLI: Download Istio CLI
ArgoCD: Install ArgoCD (Releases)
Note: For AWS CLI, kubectl, eksctl, and Istio CLI, add their respective executable folders to the PATH variable.

Steps to Deploy
1. Configure AWS CLI
Run the following command and input your AWS credentials:

bash
Copy code
aws configure
2. Create an EKS Cluster
Run the following command to create your EKS cluster:

bash
Copy code
eksctl create cluster --name devopsprojectcluster
Caution: EKS is a paid service. Refer to AWS Pricing for more details.

3. Install ArgoCD
Create a namespace for ArgoCD:
bash
Copy code
kubectl create namespace argocd
Apply the ArgoCD installation manifest:
bash
Copy code
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Verify the status of the pods:
bash
Copy code
kubectl get pods -n argocd
4. Access the ArgoCD Dashboard
Forward the ArgoCD service port:
bash
Copy code
kubectl port-forward svc/argocd-server -n argocd 8080:443
Visit the dashboard at https://localhost:8080 and log in with:
Username: admin
Password: Retrieve it using:
bash
Copy code
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
5. Deploy the Application
Clone your repository:
bash
Copy code
git clone https://github.com/your-repo/project.git
cd project
Commit and push the YAML files for your application:
bash
Copy code
git add .
git commit -m "Initial commit"
git push
In the ArgoCD dashboard, create a new application:
Name: Name of your application
Source: Git repository URL and file path
Destination: Kubernetes cluster and namespace (default by default)
Policy: Automatic synchronization
6. Install Istio
Install Istio with the demo profile:
bash
Copy code
istioctl install --set profile=demo
Enable Istio injection for the default namespace:
bash
Copy code
kubectl label namespace default istio-injection=enabled
Restart all pods:
bash
Copy code
kubectl delete pods --all -n default
Apply Istio add-ons:
bash
Copy code
kubectl apply -f addons/
7. Test the Deployment
Forward the application service port to test locally:
bash
Copy code
kubectl port-forward svc/productpage -n default 80:9080
Generate test traffic:
bash
Copy code
while :; do curl -s -o /dev/null -w "%{http_code}" http://<your-service-url>/productpage; done
8. Monitor and Manage
Use Istio’s dashboards for monitoring:
bash
Copy code
istioctl dashboard kiali
bash
Copy code
istioctl dashboard grafana
Modify traffic rules, destination rules, or virtual services in your Git repository to trigger automatic updates via ArgoCD.
9. Security Analysis
Utilize Trivy or SonarQube to scan for vulnerabilities in your Kubernetes setup and application.
Conclusion
This project allows you to practice deploying and managing cloud-native applications using industry-standard tools. ArgoCD enables GitOps workflows, while Istio provides advanced observability, traffic control, and security features for your services. This practical guide empowers you to build and manage robust and scalable systems effectively.

App-resource
Bookinfo App on GitHub
