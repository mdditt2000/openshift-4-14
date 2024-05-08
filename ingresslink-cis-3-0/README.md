## F5 IngressLink with F5 BIG-IP Next in OpenShift

The F5 IngressLink solution addresses modern app delivery at scale. IngressLink is a resource definition defined between BIG-IP Next and NGINX using F5 CIS and NGINX Ingress Controller.

F5 IngressLink is an elegant control plane solution that offers a unified method of working with both technologies from a single interface—offering the best of BIG-IP Next and NGINX and fostering better collaboration across NetOps and DevOps teams. The diagram below demonstrates this use case

This document provides a step-by-step guide how **F5 (CIS)** integrates with F5 BIG-IP Next using IngressLink. This document focuses on deploying **F5 CIS** in a standalone OpenShift 4.15 cluster. CIS is configured to detect the NGINX Ic using NodePort Mode. Additional demos will cover ClusterIP etc

Demo on YouTube [video]()

![diagram](https://github.com/mdditt2000/openshift-4-14/blob/main/ingresslink-cis-3-0/diagram/2024-05-08_10-25-08.png)

Installing CIS 3.x
------------------

Step 1: Clone the CIS repo

```shell
git clone https://github.com/mdditt2000/openshift-4-14/tree/main/ingresslink-cis-3-0
```
Step 2: Install the RBAC for CIS Controller

```shell
oc create -f clusterrole.yaml
```

Step 3: Install Custom Resource Definitions for CIS Controller

```shell
oc create -f customresourcedefinitions.yaml
```

Step 4: Install CIS Deploy config CR

```shell
oc create -f cis-config.yaml
```

Step 5: Create the kubernetes secret for Central Manager credentials

```shell
mkdir "creds"
echo -n "admin" > creds/username
echo -n "######" > creds/password
echo -n "10.192.125.177" > creds/url
kubectl create secret generic f5-bigip-ctlr-login -n kube-system --from-file=creds/ 
```

Step 6: Update the CIS deployment file with required image and parameters and install the CIS Controller.

```shell
oc create -f f5-bigip-ctlr-deployment.yaml
```

Step 6: Update NGINX IC Operator

#### Step 3 Deploy NGINX Ingress Controller in both Clusters

Deploy the NGINX Ingress Controller in **OpenShift-4-11** for ClusterIP

Getting Started [repo](https://github.com/nginxinc/nginx-ingress-helm-operator#getting-started)