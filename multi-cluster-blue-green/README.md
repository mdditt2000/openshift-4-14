## F5 integration with RedHat Advanced Cluster Management for Multi-Cluster

This document demonstrates how **F5 can integrate with RedHat Advanced Cluster Management** (ACM) in a OpenShift  Multi-Cluster environment. This document focuses on deploying **F5 Container Ingress Services (CIS)** in then management cluster where ACM is deployed. CIS will monitor PODs deployed the worker clusters and orchestrate F5 BIG-IP to forward traffic to those PODs as shown in the diagram below:

Demo on YouTube [video]()

![diagram](https://github.com/mdditt2000/openshift-4-14/blob/main/multi-cluster-blue-green/diagram/2024-04-16_14-52-14.png)

GitHub Repo [repo](https://github.com/mdditt2000/openshift-4-14/tree/main/multi-cluster-blue-green)

### Notes

If you look at the route, you'll notice that I've put the Route base service weight as 0. This will ensure that this service doesn't take any share of the overall traffic distribution. Although the pool for the base service gets created (without any pool members for this case), the traffic is distributed among the extended services.

```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  annotations:
    virtual-server.f5.com/multiClusterServices: '[
    {"clusterName": "openshift-4-15", "service": "coffee-svc", "namespace": "default", "servicePort": "8080", "weight": 100}, 
    {"clusterName": "openshift-4-15-cm", "service": "coffee-svc", "namespace": "default", "servicePort": "8080", "weight": 0}
    ]'
  labels:
    f5type: systest
  name: coffee
spec:
  host: cafe.example.com
  path: /coffee
  port:
    targetPort: 8080
  to:
    kind: Service
    name: coffee-svc
    weight: 0
```

One more point to note is that in the extendedConfigmap I have set the mode to ratio. This is because currently CIS processes the weights only in two case

```
apiVersion: v1
kind: ConfigMap
metadata:
  labels:
    f5nr: "true"
  name: global-spec-config
  namespace: kube-system
data:
  extendedSpec: |
    mode: ratio
    externalClustersConfig:
    - clusterName: openshift-4-15
      secret: default/openshift-4-15
    - clusterName: openshift-4-15-cm
      secret: default/openshift-4-15-cm
    extendedRouteSpec:
    - namespace: default
      vserverAddr: 10.192.125.65
      vserverName: cafe
      allowOverride: false
```

1) The route is an A/B route 
2) mode is set to ratio

However, the cluster ratio doesn't play any role here as we have not defined any cluster ratio values for any of the clusters. So, each cluster will have the equal weightage. Therefore, in this case the traffic distribution will be solely based on the service weights (which is the requirement for this case)


