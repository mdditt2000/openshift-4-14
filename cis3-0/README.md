## Integrating F5 CIS 3.x with F5 BIG-IP Next in OpenShift

This document provides a step-by-step guide how **F5 Container Ingress Services (CIS)** integrates with F5 BIG-IP Next. This document focuses on deploying **F5 CIS** in a standalone OpenShift 4.15 cluster. CIS is configured to detect the Node/Pods using NodePort Mode. Additional demos will cover ClusterIP etc

Demo on YouTube [video](https://youtu.be/nYBspS3vnVU)

![diagram](https://github.com/mdditt2000/openshift-4-14/blob/main/cis3-0/diagram/2024-05-01_12-30-09.png)

Installing CIS 3.x
------------------

Step 1: Clone the CIS repo

```shell
git clone https://github.com/mdditt2000/openshift-4-14/cis3-0
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