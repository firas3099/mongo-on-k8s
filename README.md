# MongoDB + Mongo Express Deployment on Kubernetes

This tutorial explains how to deploy an application composed of MongoDB and Mongo Express on a Kubernetes cluster installed with kubeadm.  
We use two Deployments, two Services (ClusterIP for MongoDB and LoadBalancer for Mongo Express), a ConfigMap for the database URL, a Secret for MongoDB credentials, and a PersistentVolume + PersistentVolumeClaim for MongoDB data persistence.

---

## Architecture Overview

The application includes:  
- A MongoDB Pod deployed via a Deployment, exposed by a ClusterIP Service.  
- A Mongo Express Pod deployed via a Deployment, exposed via a LoadBalancer Service.  
- A ConfigMap holding the MongoDB service URL.  
- A Secret containing MongoDB username and password.  
- PersistentVolume and PersistentVolumeClaim for MongoDB data persistence.

---

## 1. Creating the MongoDB Secret

The Kubernetes Secret stores the MongoDB username and password encoded in base64.  
This secures the credentials used by MongoDB and Mongo Express.

---

## 2. Creating the ConfigMap

The ConfigMap stores the MongoDB service URL, used by Mongo Express to connect to the database.

---

## 3. Deploying MongoDB

The MongoDB Deployment runs a pod with the official `mongo` image.  
It uses a persistent volume to store data, ensuring data durability across pod restarts.  
Credentials are injected from the Kubernetes Secret.

---

## 4. MongoDB Service (ClusterIP)

This service is of type ClusterIP, meaning it’s accessible only inside the Kubernetes cluster.  
It exposes the standard MongoDB port (27017) to other pods.

---

## 5. Deploying Mongo Express

The Mongo Express Deployment runs a pod with the official `mongo-express` image.  
It uses values from the Secret and ConfigMap to connect to MongoDB.

---

## 6. Mongo Express Service (LoadBalancer)

This service is of type LoadBalancer, exposing the Mongo Express web interface outside the cluster, allowing access via an external IP address.

---

## 7. MetalLB Configuration for LoadBalancer

In bare-metal environments, Kubernetes does not natively support LoadBalancer services.  
MetalLB provides IP addresses for LoadBalancer services by allocating external IPs within a configured address pool.

To install MetalLB, run the following command:

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.15.2/config/manifests/metallb-native.yaml
```
This installs the MetalLB components in your cluster.

You need to create a pool of "public" IP addresses that match your network card and are available to assign to LoadBalancer services.

For any additional information, visit https://metallb.io/.
---

## Notes

- Make sure your PersistentVolume configuration matches your storage infrastructure.  
- Kubernetes Secrets must be base64-encoded.  
- This deployment is suitable for testing or learning environments and should be adapted for production with additional configurations.

---
