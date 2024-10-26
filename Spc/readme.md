# Kubernetes ReplicaSet and NodePort Service with Kubeadm

This project demonstrates how to deploy a **Spring PetClinic** web application on a Kubernetes cluster using a ReplicaSet and expose it using a NodePort service. The application is developed in **Java** using **Spring Boot** and runs on port 8080. The Kubernetes cluster is set up using **kubeadm**.

## Table of Contents
- [Overview](#overview)
- [Application Details](#application-details)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Cluster Setup with Kubeadm](#cluster-setup-with-kubeadm)
- [Deployment](#deployment)
- [Accessing the Application](#accessing-the-application)
- [Clean Up](#clean-up)

## Overview

This project consists of:
- **ReplicaSet**: To create and manage three replicas of the **Spring PetClinic** application.
- **Service**: A NodePort service to expose the web application to external traffic.

### Key Features
- Pods created using a ReplicaSet for high availability.
- Resource requests and limits for efficient resource management.
- External access to the Spring PetClinic application via NodePort service.
![image](.\img\plan.png)
## Application Details

- **Application Name**: Spring PetClinic
- **Technology Stack**: Java, Spring Boot
- **Container Image**: `shaikkhajaibrahim/spcjan2024:1.0`
- **Container Port**: 8080

This application provides a simple web interface to manage a veterinary clinic, including functionalities to add, update, and view pets and their owners.

## Prerequisites

- Kubernetes cluster setup using **kubeadm**.
- kubectl installed and configured to interact with the Kubernetes cluster.

## Project Structure

- `replicaset.yaml`: Defines the ReplicaSet with 3 pod replicas running the Spring PetClinic web application.
- `service.yaml`: Defines the NodePort service to expose the application externally.

## Cluster Setup with Kubeadm

If you don't have a Kubernetes cluster set up, you can create one using kubeadm by following these steps:

1. **Initialize the control plane** on the master node:

   ```bash
   sudo kubeadm init --pod-network-cidr=192.168.0.0/16
   ```

2. **Set up kubectl for the master node**:

   ```bash
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

3. **Install a network plugin** (e.g., Calico):

   ```bash
   kubectl apply -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
   ```

4. **Join worker nodes** to the cluster using the `kubeadm join` command provided by the `kubeadm init` output on the master node.

## Deployment

1. **Create the ReplicaSet**:

   Apply the ReplicaSet configuration file (`replicaset.yaml`):

   ```bash
   kubectl apply -f replicaset.yaml
   ```

   The ReplicaSet will create 3 replicas of the Spring PetClinic application, running on port 8080 in each pod.

2. **Create the NodePort Service**:

   Apply the service configuration file (`service.yaml`):

   ```bash
   kubectl apply -f service.yaml
   ```

   This service exposes the application to external traffic via a randomly assigned NodePort.

## Accessing the Application

Once the deployment is complete, you can access the Spring PetClinic application using the NodePort assigned by Kubernetes. To find the NodePort:

1. Run the following command:

   ```bash
   kubectl get svc spc-svc
   ```

2. Look for the `NodePort` under the `PORT(S)` column, which will be in the format `80:<NodePort>`. For example, `80:32000/TCP`.

3. Access the application in your browser by visiting `http://<NodeIP>:<NodePort>`.

   - Replace `<NodeIP>` with the IP address of any node in your cluster.
   - Replace `<NodePort>` with the actual NodePort shown in the output.

## Clean Up

To clean up the resources created:

1. Delete the ReplicaSet:

   ```bash
   kubectl delete -f replicaset.yaml
   ```

2. Delete the Service:

   ```bash
   kubectl delete -f service.yaml
   ```
