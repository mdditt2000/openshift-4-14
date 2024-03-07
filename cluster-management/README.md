## F5 integration with RedHat Advanced Cluster Management for Multi-Cluster

This document demonstrates how **F5 can integrate with RedHat Advanced Cluster Management** (ACM) in a OpenShift  Multi-Cluster environment. This document focuses on deploying **F5 Container Ingress Services (CIS)** in then management cluster where ACM is deployed. CIS will monitor PODs deployed the worker clusters and orchestrate F5 BIG-IP to forward traffic to those PODs as shown in the diagram below:

Demo on YouTube [video]()

![diagram](https://github.com/mdditt2000/openshift-4-14/blob/main/cluster-management/diagram/2024-03-07_10-34-48.png)

GitHub Repo [repo](https://github.com/mdditt2000/openshift-4-14/tree/main/cluster-management)



