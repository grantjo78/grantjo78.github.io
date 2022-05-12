---
layout: post
title: "Azure Arc-enabled Kubernetes - Part 2: Cluster Resources"
categories: [Arc for Kubernetes]
---
Today I will be exploring being able to view Kubernetes resources for an Arc-enabled cluster.

This article contains the following sections: 
1. [The Environment](#the-environment)
2. [Kubernetes Resources](#kubernetes-resources)

## The Environment

The environment that I will be working with is described below:
1. [Hyper-V Host](#hyper-v-host)
2. [Hyper-V Guests](#hyper-v-guests)
3. [Kubernetes Environment](#kubernetes-environment)
4. [Sample Voting Application](#sample-voting-application)
5. [Arc-enabled Cluster](#arc-enabled-cluster)

### Hyper-V Host

To simulate a server that is outside of Azure, I'm going to leverage nested virtualisation within an Azure virtual machine by enabling the Hyper-V role. If you would like to know how I did this, you can review my previous post [Nested Virtualization on Azure]({% post_url 2022-04-27-hyperv-vm-deployment%}).

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-hyperv-host.jpg)

### Hyper-V Guests

Within the Hyper-V host, I've deployed two virtual machines:
- k3s-1
- k3s-2

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-hyperv-guest-k3s.jpg)


### Kubernetes Environment

For the Kubernetes environment, I'm utilising [K3s](https://k3s.io/) because it's very quick to setup and lightweight. If you're interested in how to deploy K3s and configure a cluster you can follow the [quickstart guide](https://rancher.com/docs/k3s/latest/en/quick-start/).

My cluster consists of 2 nodes, a master (k3s-1) and a worker (k3s-2).

```
k3s-admin@k3s-1:~$ kubectl get nodes

NAME    STATUS   ROLES                  AGE   VERSION
k3s-1   Ready    control-plane,master   22h   v1.22.7+k3s1
k3s-2   Ready    <none>                 22h   v1.22.7+k3s1
```

### Sample Voting Application

I've deployed the below sample appliaction into the **Azure** namespace.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-votingapp.jpg)

Below is the [sample yaml code](https://docs.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-cli#deploy-the-application) for the application (with some slight modifications for K3s)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
        env:
        - name: ALLOW_EMPTY_PASSWORD
          value: "yes"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 8080
  - targetPort: 80
  selector:
    app: azure-vote-front
```

The following illustrates the applications resources running within the K3s environment.

```
k3s-admin@k3s-1:~$ sudo kubectl get all  -n voting -o wide
NAME                                    READY   STATUS    RESTARTS   AGE    IP           NODE    NOMINATED NODE   READINESS GATES
pod/azure-vote-front-5f4d7db9c8-tgx9l   1/1     Running   0          41m    10.42.1.6    k3s-2   <none>           <none>
pod/azure-vote-back-59cb7dc555-nxnws    1/1     Running   0          41m    10.42.1.7    k3s-2   <none>           <none>
pod/svclb-azure-vote-front-hx5kn        1/1     Running   0          40m    10.42.1.8    k3s-2   <none>           <none>
pod/svclb-azure-vote-front-hx8gp        1/1     Running   0          40m    10.42.0.35   k3s-1   <none>           <none>
pod/azure-vote-front-5f4d7db9c8-hpt5l   1/1     Running   0          2m7s   10.42.1.9    k3s-2   <none>           <none>
pod/azure-vote-back-59cb7dc555-554fm    1/1     Running   0          2m7s   10.42.0.36   k3s-1   <none>           <none>

NAME                       TYPE           CLUSTER-IP      EXTERNAL-IP                 PORT(S)          AGE   SELECTOR
service/azure-vote-back    ClusterIP      10.43.160.59    <none>                      6379/TCP         41m   app=azure-vote-back
service/azure-vote-front   LoadBalancer   10.43.239.137   192.168.0.51,192.168.0.52   8080:32633/TCP   41m   app=azure-vote-front

NAME                                    DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE   CONTAINERS    IMAGES                      SELECTOR
daemonset.apps/svclb-azure-vote-front   2         2         2       2            2           <none>          41m   lb-tcp-8080   rancher/klipper-lb:v0.3.5   app=svclb-azure-vote-front

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS         IMAGES                                            SELECTOR
deployment.apps/azure-vote-front   2/2     2            2           41m   azure-vote-front   mcr.microsoft.com/azuredocs/azure-vote-front:v1   app=azure-vote-front
deployment.apps/azure-vote-back    2/2     2            2           41m   azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8         app=azure-vote-back

NAME                                          DESIRED   CURRENT   READY   AGE   CONTAINERS         IMAGES                                            SELECTOR
replicaset.apps/azure-vote-front-5f4d7db9c8   2         2         2       41m   azure-vote-front   mcr.microsoft.com/azuredocs/azure-vote-front:v1   app=azure-vote-front,pod-template-hash=5f4d7db9c8
replicaset.apps/azure-vote-back-59cb7dc555    2         2         2       41m   azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8         app=azure-vote-back,pod-template-hash=59cb7dc555

```

### Arc-enabled Cluster

In [Part 1]({% post_url 2022-05-03-arc-k8s-onboarding%}) of this series I covered the onboarding of a Kubernetes cluster into Arc. This same cluster will be used in this article.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-cluster-overview.jpg)

## Kubernetes Resources

When you select one of the resources under **Kubernetes resources** (e.g. Namespaces) for the first time you will be prompted to enter a **Service account bearer token**. This token will be used by Arc to gain access to the cluster.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-namespace-signin.jpg)

The following steps will walk through the process of creatating a token for Arc to utilise, providing visibility to the clusters resources.

### Step 1: Create Service Account

In order to obtain a bearer token, a [Service Account](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#service-account-tokens) needs to be created.

The following command is used to create a service account called **admin-user**.

```
kubectl create serviceaccount <service account name>
```
```
Example:

k3s-admin@k3s-1:~$ kubectl create serviceaccount admin-user
serviceaccount/admin-user created
```

### Step 2: Create Cluster Role Binding

Once the admin-user service account has been created it needs to be [bound to a role](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding).

The following command is used to bind the admin-user service account to the **cluster-admin** role.

```
kubectl create clusterrolebinding <role binding name> --clusterrole <cluster role to assign> --serviceaccount default:<service account name>
```
```
Example:

k3s-admin@k3s-1:~$ kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
clusterrolebinding.rbac.authorization.k8s.io/admin-user-binding created
```

### Step 3: Extract Secret Name

In this step I'm going to extract the **Secret Name** from the service account using the following command. 

```
SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
```
```
Example:
k3s-admin@k3s-1:~$ SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
```

### Step 4: Obtain Token

To obtain the token to be used, the following command will be used.

```
TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
```
```
Example:

k3s-admin@k3s-1:~$ TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
k3s-admin@k3s-1:~$ echo $TOKEN
```
![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-token.jpg)

Copy the token.

### Step 5: Paste Token

Switch back to the Azure Portal and paste the token into the **Sign in** box.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-token-paste.jpg)

Select **Sign in**.

### Step 6: Resources

Once the sign in has completed, the resources from the cluster will be visible.

#### Namespaces

All the namespaces that exist within the cluster will be visible. The [sample application](#sample-voting-application) namespace, **voting**, is highlighted.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-namespace-voting.jpg)

#### Workloads

##### Deployments

Selecting **Workloads** will bring up all the **Deployments** within the cluster.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-workloads.jpg)

The namespace filter has be used to filter deployments within the voting namespace. 

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-deployments-voting.jpg)

##### Pods

Selecting **Pods** will display all the pods in the cluster. Filtering the view by the voting namespace will display the pods that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-pods-voting.jpg)

##### Replica Sets

Selecting **Replica sets** will display all the replica sets in the cluster. Filtering the view by the voting namespace will display the replica sets that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-replicasets-voting.jpg)


#### Service and ingresses

Selecting the **Services and ingresses** resource will display all services in the cluster.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-services.jpg)

Filtering the view by the voting namespace will display the services that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-service-voting.jpg)

#### Storage

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-storage.jpg)

#### Configuration

##### Config maps

Selecting the **Configuration** resource will display all the config maps in the cluster.

Filtering the view by the voting namespace will display the config maps that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-config-configmaps.jpg)

##### Secrets

Selecting **Secrets** will display all the secrets in the cluster.

Filtering the view by the voting namespace will display the secrets that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-config-secrets.jpg)