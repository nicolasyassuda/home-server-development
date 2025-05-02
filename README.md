# Main deployment (MICROK8S)
This Repositorie is focused in creating a enviroment to test and develop in home-servers

1. Install snapd:
   - Update your package list:  
     ```
     sudo apt-get update
     ```
   - Install snapd:  
     ```
     sudo apt-get install -y snapd
     ```
   - Enable and start snapd service:  
     ```
     sudo systemctl enable snapd  
     sudo systemctl start snapd
     ```
   - Initialize the core snap environment:
     ```
     sudo snap install core
     sudo snap refresh core
     ```

2. Install MicroK8s via snap:
   - Install MicroK8s as a strict-confined snap:  
     ```sudo snap install microk8s --classic```

If you want to use the website interface a recommended you to use these addons:

```
addons:
  enabled:
    cert-manager         # (core) Cloud native certificate management
    dashboard            # (core) The Kubernetes dashboard
    dns                  # (core) CoreDNS
    ha-cluster           # (core) Configure high availability on the current node
    helm                 # (core) Helm - the package manager for Kubernetes
    helm3                # (core) Helm 3 - the package manager for Kubernetes
    hostpath-storage     # (core) Storage class; allocates storage from host directory
    ingress              # (core) Ingress controller for external access
    metrics-server       # (core) K8s Metrics Server for API access to service metrics
    storage              # (core) Alias to hostpath-storage add-on, deprecated
```
And you can choose your preference dashboard i'm using Rancher dashboard because is more visual and deliver more tools to manage the kubernetes.
Use this tutorial to install rancher with you want (you need to pay attention in the commands because some of this commands can't work, but is very easy to adjust):
https://suda.pl/5-minute-home-server-with/


