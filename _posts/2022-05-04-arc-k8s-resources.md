---
layout: post
title: "Azure Arc-enabled Kubernetes - Part 3: Viewing Cluster Resources"
categories: [Arc for Kubernetes]
---
In [Part 2: Onboarding a Kubernetes Cluster]({% post_url 2022-05-03-arc-k8s-onboarding%}) I onboarded a K3s cluster into Azure Arc. I'll now explore the clusters resources from Arc. 
- [Viewing Kubernetes Resources](#viewing-kubernetes-resources)
- [What's Next](#whats-next)

## Viewing Kubernetes Resources

When you select one of the resources under **Kubernetes resources** (e.g. Namespaces) for the first time you will be prompted to enter a **Service account bearer token**. This token will be used by Arc to gain access to the cluster.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-namespace-signin.jpg)

The following steps will walk through the process of creatating a token for Arc to utilise, providing visibility to the clusters resources:
- [Step 1: Create Service Account](#step-1-create-service-account)
- [Step 2: Create Cluster Role Binding](#step-2-create-cluster-role-binding)
- [Step 3: Extract Secret Name](#step-3-extract-secret-name)
- [Step 4: Obtain Token](#step-4-obtain-token)
- [Step 5: Paste Token](#step-5-paste-token)
- [Step 6: Resources](#step-6-resources)


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

### Step 6: View Resources

Once the sign in has completed, the resources from the cluster will be visible.

#### Namespaces

All the namespaces that exist within the cluster will be visible. The [sample application](#sample-voting-application) namespace, **voting**, is highlighted.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-namespace-voting.jpg)

#### Workloads

##### Deployments

Selecting **Workloads** will bring up all the **Deployments** within the cluster.

Filtering the view by the voting namespace will display the deploymets for the voting applications. 

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-deployments-voting.jpg)

##### Pods

Selecting **Pods** will display all the pods in the cluster. Filtering the view by the voting namespace will display the pods that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-pods-voting.jpg)

##### Replica Sets

Selecting **Replica sets** will display all the replica sets in the cluster. Filtering the view by the voting namespace will display the replica sets that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-replicasets-voting.jpg)


#### Service and ingresses

Selecting the **Services and ingresses** resource will display all services in the cluster.

Filtering the view by the voting namespace will display the services that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-service-voting.jpg)

#### Configuration

##### Config maps

Selecting the **Configuration** resource will display all the config maps in the cluster.

Filtering the view by the voting namespace will display the config maps that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-config-configmaps.jpg)

##### Secrets

Selecting **Secrets** will display all the secrets in the cluster.

Filtering the view by the voting namespace will display the secrets that make up the voting application.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resource-config-secrets.jpg)

## What's Next

Now that have explored viewing the resources within an Arc-enabled Kubernetes cluster we can move onto [Part 4: Monitoring Cluster Performance]({% post_url 2022-05-06-arc-k8s-monitoring%}).