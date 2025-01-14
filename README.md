# furbar
cka projects follow along

# Kubernetes Deployment Configuration Project

Welcome to the Kubernetes Deployment Configuration project! This guide will help you set up a Kubernetes environment using Kustomize overlays, Helm charts, and ArgoCD for GitOps-style continuous delivery.

This project is a follow-along guide based on the [CKA Study Guide Repository](https://github.com/alvo254/cka-study). It builds on similar concepts to manage Kubernetes clusters with tools like ArgoCD and Helm.

---

## **Structure of the Repository**

The repository is structured into packages and overlays, enabling different deployment environments and reusable Kubernetes manifests.

```
.
├── cluster.yaml                      # Main Kubernetes cluster configuration file
├── overlays/                         # Environment-specific overlays
│   ├── dev/                          # Development overlay
│   │   ├── kustomization.yaml        # Kustomize configuration for dev
│   │   └── patches/                  # Patches for modifying base manifests
│   │       └── frontend-dev-patch.yaml
│   └── prod/                         # Production overlay (empty for now)
├── pkg1/                             # Application package 1
│   ├── app.yaml                      # Main application manifest
│   └── manifests/                    # Kubernetes manifests
│       ├── clusterRole.yaml          # ClusterRole definition
│       ├── frontend-deploy.yaml      # Frontend deployment manifest
│       ├── networking.yaml           # Networking policies
│       ├── restrict-images.yaml      # Image restriction policy
│       └── validate-resources.yaml   # Resource validation policy
└── README.md                         # This file
```

---

## **Key Components**

### **cluster.yaml**

This file contains the core cluster configuration settings. It defines the Kubernetes cluster resources and their specifications.

### **Overlays**

Overlays are used to customize the base manifests for different environments (e.g., development, production). The `kustomization.yaml` files specify how the patches are applied to the base resources.

### **Manifests**

Located in the `pkg1/manifests/` directory, these YAML files define Kubernetes objects like Deployments, ClusterRoles, and Networking policies.

---

## **Setting Up ArgoCD**

ArgoCD is used to manage GitOps-based continuous delivery for the Kubernetes cluster.

### **Installing ArgoCD with Helm**

1. **Create the** `**argocd**` **namespace:**
    
    ```
    kubectl create namespace argocd
    ```
    
2. **Install ArgoCD using Helm:**
    
    ```
    helm install argocd argo/argo-cd --namespace argocd \
        --set server.service.type=NodePort
    ```
    
3. **Retrieve the initial admin password:**
    
    ```
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```
    

---

## **Installing Cilium with Helm**

Cilium is a networking, observability, and security solution for Kubernetes.

### **Install Cilium with the following command:**

```
helm install cilium cilium/cilium --version 1.16.1 \
    --namespace kube-system \
    --set tunnel=disabled \
    --set bpf.masquerade=false \
    --set prometheus.enabled=true \
    --set hubble.enabled=true \
    --set hubble.metrics.enabled="{dns,drop,tcp,flow,port-distribution,icmp,http}" \
    --set hubble.relay.enabled=true \
    --set hubble.ui.enabled=true \
    --set hubble.peer.target="hubble-peer.kube-system.svc.cluster.local:4244" \
    --set directRoutingDevice=eth0
```

For a detailed guide on how to install Cilium, visit the official Cilium Installation Documentation.

---

## **Setting Up a Kind Cluster**

We are using a Kind (Kubernetes IN Docker) cluster for this project. Follow the official guide to set up a Kind cluster on your local machine:

- Kind Installation Guide
    

---

## **Installing kubectl**

Ensure you have `kubectl` installed to interact with your Kubernetes cluster. You can find installation instructions here:

- kubectl Installation Guide
    

---

## **Add Kyverno Helm Repository:**

Kyverno is a Kubernetes-native policy management tool.

```
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
```

---

## **Accessing the ArgoCD Web UI**

Once ArgoCD is deployed, access the web UI to manage your applications:

1. **Get the ArgoCD server service details:**
    
    ```
    kubectl -n argocd get svc argocd-server
    ```
    
2. **Access the web UI:**
    
    - Use the NodePort or LoadBalancer IP to connect to the ArgoCD web interface.
        
    - The default username is `admin`. Use the retrieved password from the secret.
        

---

## **Deploying Applications with ArgoCD**

1. **Create an ArgoCD Application:** Define an application that points to your Git repository and uses Kustomize for deployments.
    
2. **Synchronize the Application:** Sync the application to deploy the resources to your Kubernetes cluster.
    

---

## **Customizing Your Setup**

### **Modifying Overlays**

To customize the deployments for specific environments, modify the `kustomization.yaml` files and patches within the respective overlay directories.

### **Adding New Packages**

To add a new package, create a directory under `pkg1/` with the necessary Kubernetes manifests.

---

## **Contributing**

We welcome contributions to improve the repository. Follow these steps:

1. Fork the repository.
    
2. Create a new branch:
    
    ```
    git checkout -b feature/YourFeature
    ```
    
3. Commit your changes:
    
    ```
    git commit -m 'Add YourFeature'
    ```
    
4. Push your changes:
    
    ```
    git push origin feature/YourFeature
    ```
    
5. Open a Pull Request.