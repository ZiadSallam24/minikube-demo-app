# Minikube Kubernetes Deployment Showcase

Originally from Nana Janashia

YouTube Video: https://www.youtube.com/watch?v=X48VuDVv0do

## Project Overview

This repository demonstrates key Kubernetes deployment concepts using Minikube for local development. It contains two independent projects:

MongoDB Stack: Deploying a secure database and a web UI (Mongo Express) using Secrets and ConfigMaps.

Dashboard Ingress: Exposing the default Kubernetes Dashboard via an Ingress resource and the Nginx controller, demonstrating host-based routing.

## Prerequisites ⚙️
To run these projects, you must have the following tools installed and configured:

Git

Minikube (Tested with Docker driver)

kubectl

A Unix-like terminal (for Base64 commands)

## Project 1: Secured MongoDB and Mongo Express Stack
Goal
To deploy a functional MongoDB instance and a Mongo Express web administration interface into a dedicated namespace (myns), using Kubernetes Secrets to handle sensitive credentials and a ConfigMap for non-sensitive UI configuration.

Deployment Files
mongo-deploy.yaml

mongo-secret.yaml

mongo-express.yaml

mexpress-secret.yaml

app-configmap.yaml

### Setup Instructions 
Start Cluster and Create Namespace:
Start your Minikube cluster and create the dedicated namespace for this application:

```bash
minikube start
kubectl create namespace myns
```
Configure Credentials (IMPORTANT: Secrets):
For security, you must manually generate Base64 encoded values for your desired MongoDB/Mongo-Express username and password and place them into the respective YAML files.

Generate Base64 values:

1. Generate Base64 for your chosen username/password
```bash   
echo -n 'YOUR_MONGO_USERNAME' | base64
echo -n 'YOUR_MONGO_PASSWORD' | base64
```
Edit Files: Open mongo-secret.yaml and mexpress-secret.yaml. Replace the placeholder Base64 strings under the data: section with the values generated above.

Apply All Configuration Files:
Apply the files in the recommended order (Secrets first, then Deployments/ConfigMaps):

Apply Secrets (credentials)
```bash
kubectl apply -f mongo-secret.yaml -f mexpress-secret.yaml
```
Apply Deployments and ConfigMap
```bash
kubectl apply -f mongo-deploy.yaml -f mongo-express.yaml -f app-configmap.yaml
```
Verify Pods:
Wait a moment, then confirm that all resources (Pods, Services, etc.) are running successfully:
```bash
kubectl get all -n myns
```
Access Mongo Express Web UI:
Use the minikube service command to open the Mongo Express application in your local browser:

minikube service mongo-express-service -n myns

The browser should automatically open, displaying the Mongo Express login page.

## Project 2: Kubernetes Dashboard with Ingress
Goal
To expose the Kubernetes Dashboard service using an Ingress resource, allowing access via a custom domain name (dashboard.com) configured for host-based routing.

Deployment Files
ingress.yaml

Setup Instructions 🚀
Enable Ingress Controller:
Ensure the necessary Nginx Ingress Controller addon is enabled in Minikube:
```bash
minikube addons enable ingress
```
Start the Kubernetes Dashboard:
Run the dashboard command to ensure the necessary services are running in the kubernetes-dashboard namespace:
```bash
minikube dashboard &
```
(The & allows the command to run in the background if desired.)

Map Hostname to Minikube IP (Local Configuration):
The Ingress controller routes traffic based on the hostname. You must modify your local machine's hosts file to resolve dashboard.com to your Minikube Node's IP address.

Get Minikube IP:
```bash
minikube ip
```
Update Hosts File: Add the following line to your system's hosts file (e.g., /etc/hosts):

<YOUR_MINIKUBE_IP>  dashboard.com

(Replace <YOUR_MINIKUBE_IP> with the actual IP address retrieved above.)

Apply Ingress Configuration:
Apply the Ingress rule to the kubernetes-dashboard namespace:
```bash
kubectl apply -f ingress.yaml -n kubernetes-dashboard
```
Access the Dashboard:
Verify the Ingress resource is created and then open the custom URL in your browser:
```bash
kubectl get ingress -n kubernetes-dashboard
```
Navigate to:

[http://dashboard.com](http://dashboard.com)

The Kubernetes Dashboard login page should now load, proving the Ingress routing is functional.

## Learning Reflection
This project was a hands-on exercise to explore and gain a fundamental understanding of core Kubernetes concepts:

Database Deployment: Deployed a MongoDB instance using a standard Deployment resource. It is important to note that for robust, stateful production environments, a StatefulSet would be the more appropriate tool, but a Deployment was used here for initial setup simplicity.

Security & Config: Practiced implementing application configurations using Secrets for sensitive credentials and ConfigMaps for general settings.

Networking Exploration: Gained experience exposing internal services:

Using Minikube's LoadBalancer abstraction (via the minikube service command) to access Mongo Express.

Implementing Ingress and enabling the Nginx Ingress Controller addon for advanced host-based routing of the Kubernetes Dashboard.
