# Lamassu for Kubernetes

The aim of this repository is to help during of Lamassu in a prodcution envrionment and to provide some tips that can help troubleshoot potential erros.

## 1. Kubernetes Requirements 

In order to properly run Lamassu, make sure you have a kuebernetes cluster v1.21.2 or higher. If your cluster is managed by some cloud provider, make sure to follow the steps descrived by each kubernetes component.

This guide uses some third party tools and applications in order to succesfully deploy the application. Please make sure you have the following applications. Otherwise follow the instructions below.

### Helm v3

Helm is a tool used to manage Kubernetes applications. This tool will be used to deploy different components of Lamassus's architecture. Make sure you have this tool installed, otherwise visit Helm's website: https://helm.sh/

### Kubernetes Nginx Ingress Controller

https://kubernetes.github.io/ingress-nginx/deploy/

Kubernetes does not provide a default ingress controller. There are different alternatives that may be used, but the scripts and yaml files on this repo are based on the nginx ingress controller.


In order to install this component, run the following command:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.4/deploy/static/provider/cloud/deploy.yaml
```

### Kubernetes Cert Manager

https://cert-manager.io/docs/

Securing the communications between applications and the end user is a top priority. Cert Manager is a convinient tool that can be used to manage the lifecycle of certificate for kubernetes resources. Follow the steps below to succesfully install the component:

```
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.6.0/cert-manager.yaml
```

## 2. Deploying Lamassu

This section will guide you through the steps required to deploy Lamassu into a Kubernetes cluster

### 2.1 Generating the certificates used by kubernetes resources

As described earlier, securing traffic between components and the internet is essential. The proposed certificate structure can be seen below, but in essence, all Lamassu's applications relies on the same CA.  

![](./docs/img/cert_structure.png)

Run the following scripts to create the basic resources

```
kubectl apply -f certs/selfsigned-cert-manager 
```

The following resources should've been created:

```
kubectl get secret root-ca-secret -n lamassu-ns
```
![](./docs/img/get_root_ca_secret.png)

The contents of the secret are encoded in base64. For instance, to get the CA certificate, run the following command:

```
kubectl get secrets root-ca-secret -n lamassu-ns -o 'go-template={{index .data "ca.crt"}}' | base64 -D
```

![](./docs/img/decode_root_ca_secret.png)

## 3. Exploring Lamassu's monitoring services
