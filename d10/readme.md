🧱 Kubernetes Basic Setup Guide
1. Setup Cluster
# Install Minikube & start cluster
minikube start --driver=docker
kubectl get nodes

2. Work with Pods
# Create Pod directly
kubectl run nginx --image=nginx

# Or via YAML
kubectl apply -f nginx-pod.yaml

# Check status
kubectl get pods

3. Deployments
# Create Deployment
kubectl apply -f nginx-deploy.yaml

# Scale replicas
kubectl scale deployment nginx-deployment --replicas=3

# Update image
kubectl set image deployment/nginx-deployment nginx=nginx:1.26

4. Services
# Expose Deployment
kubectl expose deployment nginx-deployment --type=NodePort --port=80

# Get service info
kubectl get svc

# Access via browser
minikube ip

5. Namespaces
# Create namespace
kubectl create namespace dev

# Deploy in namespace
kubectl apply -f nginx-deploy.yaml -n dev

# Switch namespace
kubectl config set-context --current --namespace=dev


✅ Summary:

Pod → Single container instance

Deployment → Manages replicas & updates

Service → Exposes app internally or externally

Namespace → Logical separation of resources


----------------

Kubernetes Service – Short Summary
Why expose a Deployment?

Pods get dynamic IPs that change when recreated.

A Service provides a stable network endpoint and load balancing across Pods.

How it works

When you run:

kubectl expose deployment nginx-deployment --type=NodePort --port=80


Kubernetes creates a Service that:

Selects all Pods with label app=nginx.

Assigns a stable ClusterIP.

(For NodePort) Opens a port on each node to allow external access.

Service Types
Type	Access	Use Case
ClusterIP	Inside cluster only	Pod-to-Pod communication
NodePort	From outside via node IP	Access from browser or curl
LoadBalancer	Public cloud only	Internet-facing apps
Example (NodePort Flow)
Browser → Minikube IP:NodePort → Service (ClusterIP) → Nginx Pods


Even if Pods restart, the Service IP and port stay the same.

✅ Conclusion:
kubectl expose gives your Deployment a stable, load-balanced network endpoint for reliable communication inside or outside the cluster.



Traffic path:
Your Browser (http://192.168.49.2:31234)
        ↓
Minikube NodePort (31234)
        ↓
Kubernetes Service (ClusterIP)
        ↓
nginx Pods (randomly load-balanced)


So even if your Pods die and new ones come up,
the Service IP + Port stay the same — traffic continues smoothly


| Type                    | Accessible From                | Logic / Behavior                                                                                                        |
| ----------------------- | ------------------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| **ClusterIP** (default) | Only inside the cluster        | Creates a **virtual IP** (like 10.96.x.x) that load-balances traffic among all Pods. Used for Pod-to-Pod communication. |
| **NodePort**            | From outside via any node’s IP | Opens a **port (30000–32767)** on every cluster node, which forwards traffic to the Service → Deployment → Pods.        |
| **LoadBalancer**        | Public internet (cloud only)   | Requests a cloud provider’s external load balancer (like AWS ELB) that sends traffic into the NodePort service.         |
| **ExternalName**        | DNS alias only                 | Maps the service name to an external DNS (no real Pod backend).                                                         |
