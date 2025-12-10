---
layout: post
title: "Kubernetes for DevOps Engineers: A Practical Guide"
date: 2025-01-07
categories: [devops, orchestration]
tags: [kubernetes, k8s, containers, orchestration, devops, tutorial, beginner-friendly, hands-on, pods, deployments, services]
---

Kubernetes orchestrates containerised applications across clusters of machines. It handles deployment, scaling, networking and recovery automatically. This guide introduces Kubernetes concepts, architecture and commands. It provides practical examples so that you can follow along in your own environment.

---

## 1. What Is Kubernetes and Why Does It Exist

Docker containers solve the problem of packaging applications consistently. However, running containers in production creates new challenges. You need to distribute containers across multiple servers, restart them when they fail, scale them based on demand and manage networking between them.

Kubernetes solves these operational problems. It provides a platform for deploying and managing containerised applications at scale. Engineers define the desired state of their applications using YAML files. Kubernetes continuously works to maintain that state.

Key problems Kubernetes solves:

- Distributing containers across multiple servers
- Restarting failed containers automatically
- Scaling applications up or down based on load
- Managing network traffic between containers
- Rolling out updates without downtime
- Managing configuration and secrets securely
- Coordinating storage across containers

Kubernetes originated at Google and is now maintained by the Cloud Native Computing Foundation. It has become the standard platform for container orchestration.

---

## 2. Core Concepts

Kubernetes uses several key components to manage applications.

### Cluster

A cluster is a set of machines (nodes) that run containerised applications. One or more control plane nodes manage the cluster. Worker nodes run the application containers.

### Node

A node is a machine in the cluster. It can be a physical server, virtual machine or cloud instance. Nodes run pods and report their status to the control plane.

### Pod

A pod is the smallest deployable unit in Kubernetes. It contains one or more containers that share storage and network resources. Pods are ephemeral. When a pod fails, Kubernetes creates a new one.

### Deployment

A deployment manages a set of identical pods. It ensures the desired number of pods are running and handles updates. Deployments are the standard way to run stateless applications.

### Service

A service provides a stable network endpoint for accessing pods. Pods have temporary IP addresses that change when they restart. Services provide a consistent way to reach pods regardless of their location.

### Namespace

A namespace provides logical isolation within a cluster. Different teams or applications can use separate namespaces to avoid conflicts.

### ConfigMap and Secret

ConfigMaps store configuration data. Secrets store sensitive information such as passwords and API keys. Both can be mounted into pods as files or exposed as environment variables.

### Volume

Volumes provide persistent storage for pods. When a pod restarts, data in volumes persists.

### Ingress

An ingress manages external access to services. It provides HTTP and HTTPS routing, load balancing and SSL termination.

---

## 3. Kubernetes Architecture

A Kubernetes cluster consists of two types of nodes.

### Control Plane

The control plane manages the cluster. It makes decisions about scheduling, detects failures and responds to events. The control plane includes:

- **API Server**: The front end for the Kubernetes control plane. All commands go through it.
- **Scheduler**: Assigns pods to nodes based on resource requirements and constraints.
- **Controller Manager**: Runs controllers that maintain the desired state of the cluster.
- **etcd**: A distributed key-value store that holds all cluster data.

### Worker Nodes

Worker nodes run application containers. Each worker node includes:

- **Kubelet**: An agent that ensures containers are running in pods.
- **Container Runtime**: The software that runs containers, usually Docker or containerd.
- **Kube Proxy**: Manages network rules for pod communication.

---

## 4. Getting Started with Kubernetes

### Local Development

Several tools let you run Kubernetes locally for development and testing.

**Minikube**

Minikube runs a single-node cluster inside a virtual machine.

**Install Minikube:**

Follow the instructions at [minikube.sigs.k8s.io](https://minikube.sigs.k8s.io/docs/start/)

**Start a cluster:**

```bash
minikube start
```

**Check status:**

```bash
minikube status
```

**Stop the cluster:**

```bash
minikube stop
```

**Delete the cluster:**

```bash
minikube delete
```

**Kind (Kubernetes in Docker)**

Kind runs Kubernetes clusters using Docker containers as nodes.

**Install Kind:**

Follow the instructions at [kind.sigs.k8s.io](https://kind.sigs.k8s.io/docs/user/quick-start/)

**Create a cluster:**

```bash
kind create cluster
```

**Delete the cluster:**

```bash
kind delete cluster
```

**Docker Desktop**

Docker Desktop includes Kubernetes. Enable it in the settings.

**Rancher Desktop**

Rancher Desktop provides both Docker and Kubernetes. It works on Windows, macOS and Linux.

### Cloud Options

Managed Kubernetes services handle the control plane for you. You only manage worker nodes and applications.

- **AWS Elastic Kubernetes Service (EKS)**
- **Azure Kubernetes Service (AKS)**
- **Google Kubernetes Engine (GKE)**
- **DigitalOcean Kubernetes**
- **Linode Kubernetes Engine**
- **Oracle Cloud Infrastructure Container Engine for Kubernetes**

These services simplify cluster management and integrate with other cloud services.

---

## 5. Installing kubectl

kubectl is the command-line tool for interacting with Kubernetes clusters.

**Install kubectl:**

Follow the instructions at [kubernetes.io/docs/tasks/tools/](https://kubernetes.io/docs/tasks/tools/)

**Verify installation:**

```bash
kubectl version --client
```

**Check cluster connection:**

```bash
kubectl cluster-info
```

**View cluster nodes:**

```bash
kubectl get nodes
```

---

## 6. Basic kubectl Commands

These commands allow you to inspect and manage resources in your cluster.

**View all resources:**

```bash
kubectl get all
```

**View pods:**

```bash
kubectl get pods
```

**View pods in all namespaces:**

```bash
kubectl get pods --all-namespaces
```

**View deployments:**

```bash
kubectl get deployments
```

**View services:**

```bash
kubectl get services
```

**View detailed information:**

```bash
kubectl describe pod <pod-name>
```

**View logs:**

```bash
kubectl logs <pod-name>
```

**Follow logs in real time:**

```bash
kubectl logs -f <pod-name>
```

**Execute a command in a pod:**

```bash
kubectl exec -it <pod-name> -- bash
```

**Delete a resource:**

```bash
kubectl delete pod <pod-name>
```

---

## 7. Running Your First Application

Create a deployment using the command line.

**Run Nginx:**

```bash
kubectl create deployment nginx --image=nginx
```

**Check the deployment:**

```bash
kubectl get deployments
```

**Check the pods:**

```bash
kubectl get pods
```

**Expose the deployment as a service:**

```bash
kubectl expose deployment nginx --port=80 --type=NodePort
```

**Check the service:**

```bash
kubectl get services
```

**Access the service:**

For Minikube:

```bash
minikube service nginx
```

This opens Nginx in your browser.

**Delete the deployment and service:**

```bash
kubectl delete deployment nginx
kubectl delete service nginx
```

---

## 8. Working with YAML Files

YAML files define Kubernetes resources declaratively. This is the standard way to manage applications in production.

### Creating a Deployment

**nginx-deployment.yaml:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
```

**Apply the configuration:**

```bash
kubectl apply -f nginx-deployment.yaml
```

**Check the deployment:**

```bash
kubectl get deployments
```

**Check the pods:**

```bash
kubectl get pods
```

You should see three Nginx pods running.

**Update the deployment:**

Edit the YAML file to change `replicas: 3` to `replicas: 5`, then apply again:

```bash
kubectl apply -f nginx-deployment.yaml
```

Kubernetes creates two additional pods.

**Delete the deployment:**

```bash
kubectl delete -f nginx-deployment.yaml
```

---

## 9. Understanding Deployment Manifests

A deployment manifest contains several sections.

### apiVersion

Specifies the API version for the resource.

```yaml
apiVersion: apps/v1
```

### kind

Defines the type of resource.

```yaml
kind: Deployment
```

Common kinds include Deployment, Service, ConfigMap, Secret, Pod, Ingress, StatefulSet, DaemonSet and Job.

### metadata

Contains identifying information such as name, namespace and labels.

```yaml
metadata:
  name: my-app
  namespace: production
  labels:
    app: my-app
    version: "1.0"
```

### spec

Defines the desired state of the resource. The structure varies by resource type.

For deployments:

```yaml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-app:1.0
        ports:
        - containerPort: 8080
```

---

## 10. Creating Services

Services provide stable network endpoints for accessing pods.

**nginx-service.yaml:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer
```

**Apply the service:**

```bash
kubectl apply -f nginx-service.yaml
```

**Check the service:**

```bash
kubectl get services
```

### Service Types

**ClusterIP** (default): Exposes the service on an internal IP. Only accessible within the cluster.

```yaml
type: ClusterIP
```

**NodePort**: Exposes the service on each node's IP at a static port.

```yaml
type: NodePort
```

**LoadBalancer**: Creates an external load balancer. Available in cloud environments.

```yaml
type: LoadBalancer
```

**ExternalName**: Maps the service to a DNS name.

```yaml
type: ExternalName
```

---

## 11. Working with Namespaces

Namespaces provide logical isolation within a cluster.

**List namespaces:**

```bash
kubectl get namespaces
```

**Create a namespace:**

```bash
kubectl create namespace dev
```

**Create a namespace from YAML:**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: staging
```

```bash
kubectl apply -f namespace.yaml
```

**Deploy to a specific namespace:**

```bash
kubectl apply -f deployment.yaml -n dev
```

**View resources in a namespace:**

```bash
kubectl get pods -n dev
```

**Set default namespace:**

```bash
kubectl config set-context --current --namespace=dev
```

**Delete a namespace:**

```bash
kubectl delete namespace dev
```

Deleting a namespace removes all resources inside it.

---

## 12. ConfigMaps and Secrets

ConfigMaps and Secrets manage configuration data.

### ConfigMaps

**Create a ConfigMap from literals:**

```bash
kubectl create configmap app-config --from-literal=APP_ENV=production --from-literal=LOG_LEVEL=info
```

**Create from a file:**

```bash
kubectl create configmap app-config --from-file=config.properties
```

**Create from YAML:**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: production
  LOG_LEVEL: info
  config.json: |
    {
      "database": "postgres",
      "cache": "redis"
    }
```

```bash
kubectl apply -f configmap.yaml
```

**Use ConfigMap in a deployment:**

```yaml
spec:
  containers:
  - name: app
    image: my-app:1.0
    env:
    - name: APP_ENV
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_ENV
```

**Mount ConfigMap as a volume:**

```yaml
spec:
  containers:
  - name: app
    image: my-app:1.0
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
```

### Secrets

**Create a Secret:**

```bash
kubectl create secret generic db-secret --from-literal=username=admin --from-literal=password=secret123
```

**Create from YAML:**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=
  password: c2VjcmV0MTIz
```

Values must be base64 encoded.

**Encode values:**

```bash
echo -n 'admin' | base64
echo -n 'secret123' | base64
```

**Apply the secret:**

```bash
kubectl apply -f secret.yaml
```

**Use Secret in a deployment:**

```yaml
spec:
  containers:
  - name: app
    image: my-app:1.0
    env:
    - name: DB_USERNAME
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: username
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: password
```

---

## 13. Persistent Volumes

Pods are ephemeral. Persistent volumes provide storage that survives pod restarts.

### PersistentVolume (PV)

A PersistentVolume is a piece of storage in the cluster. Administrators create PVs manually or provision them dynamically.

**Example PV:**

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-data
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  hostPath:
    path: /data
```

### PersistentVolumeClaim (PVC)

A PersistentVolumeClaim requests storage. Kubernetes binds the PVC to a suitable PV.

**Example PVC:**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-data
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

**Apply the PVC:**

```bash
kubectl apply -f pvc.yaml
```

**Use PVC in a deployment:**

```yaml
spec:
  containers:
  - name: app
    image: my-app:1.0
    volumeMounts:
    - name: data-volume
      mountPath: /app/data
  volumes:
  - name: data-volume
    persistentVolumeClaim:
      claimName: pvc-data
```

### Storage Classes

Storage classes define different types of storage. Cloud providers offer storage classes for different performance characteristics.

**List storage classes:**

```bash
kubectl get storageclass
```

**Dynamic provisioning:**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-dynamic
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: standard
  resources:
    requests:
      storage: 5Gi
```

Kubernetes creates the PV automatically.

---

## 14. Labels and Selectors

Labels are key-value pairs attached to resources. Selectors filter resources by labels.

**Add labels to a deployment:**

```yaml
metadata:
  labels:
    app: my-app
    tier: backend
    environment: production
```

**View resources by label:**

```bash
kubectl get pods -l app=my-app
kubectl get pods -l tier=backend,environment=production
```

**Add label to an existing resource:**

```bash
kubectl label pod my-pod version=1.0
```

**Remove a label:**

```bash
kubectl label pod my-pod version-
```

Services use selectors to find pods:

```yaml
spec:
  selector:
    app: my-app
    tier: backend
```

---

## 15. Rolling Updates and Rollbacks

Kubernetes supports zero-downtime updates.

**Update a deployment:**

Edit your deployment YAML to change the image version:

```yaml
spec:
  containers:
  - name: my-app
    image: my-app:2.0
```

**Apply the change:**

```bash
kubectl apply -f deployment.yaml
```

Kubernetes gradually replaces old pods with new ones.

**Watch the rollout:**

```bash
kubectl rollout status deployment/my-app
```

**View rollout history:**

```bash
kubectl rollout history deployment/my-app
```

**Roll back to the previous version:**

```bash
kubectl rollout undo deployment/my-app
```

**Roll back to a specific revision:**

```bash
kubectl rollout undo deployment/my-app --to-revision=2
```

**Pause a rollout:**

```bash
kubectl rollout pause deployment/my-app
```

**Resume a rollout:**

```bash
kubectl rollout resume deployment/my-app
```

---

## 16. Scaling Applications

**Scale manually:**

```bash
kubectl scale deployment my-app --replicas=5
```

**Scale using YAML:**

Edit the deployment file to change `replicas: 5`, then apply:

```bash
kubectl apply -f deployment.yaml
```

### Horizontal Pod Autoscaler

The Horizontal Pod Autoscaler automatically scales pods based on CPU or memory usage.

**Create an autoscaler:**

```bash
kubectl autoscale deployment my-app --cpu-percent=70 --min=2 --max=10
```

This maintains CPU usage around 70% by scaling between 2 and 10 pods.

**View autoscalers:**

```bash
kubectl get hpa
```

**Autoscaler YAML:**

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

---

## 17. Health Checks

Kubernetes uses probes to check container health.

### Liveness Probe

Checks if the container is running. Kubernetes restarts containers that fail liveness probes.

```yaml
spec:
  containers:
  - name: app
    image: my-app:1.0
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 30
      periodSeconds: 10
```

### Readiness Probe

Checks if the container is ready to accept traffic. Kubernetes removes pods from service endpoints if readiness probes fail.

```yaml
spec:
  containers:
  - name: app
    image: my-app:1.0
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 5
```

### Startup Probe

Checks if the application has started. Useful for slow-starting containers.

```yaml
spec:
  containers:
  - name: app
    image: my-app:1.0
    startupProbe:
      httpGet:
        path: /startup
        port: 8080
      failureThreshold: 30
      periodSeconds: 10
```

### Probe Types

**HTTP GET**: Makes an HTTP request.

```yaml
httpGet:
  path: /health
  port: 8080
```

**TCP Socket**: Opens a TCP connection.

```yaml
tcpSocket:
  port: 8080
```

**Exec**: Runs a command.

```yaml
exec:
  command:
  - cat
  - /tmp/healthy
```

---

## 18. Resource Requests and Limits

Resource requests and limits control CPU and memory allocation.

```yaml
spec:
  containers:
  - name: app
    image: my-app:1.0
    resources:
      requests:
        memory: "256Mi"
        cpu: "250m"
      limits:
        memory: "512Mi"
        cpu: "500m"
```

**Requests**: Minimum resources guaranteed to the container. The scheduler uses requests to place pods on nodes.

**Limits**: Maximum resources the container can use. Kubernetes throttles CPU and kills pods that exceed memory limits.

**CPU units**: 1000m = 1 CPU core. 250m = 0.25 cores.

**Memory units**: Mi (mebibytes), Gi (gibibytes), M (megabytes), G (gigabytes).

---

## 19. Ingress

Ingress manages external HTTP and HTTPS access to services.

**Enable ingress on Minikube:**

```bash
minikube addons enable ingress
```

**Ingress example:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-service
            port:
              number: 80
```

**Apply the ingress:**

```bash
kubectl apply -f ingress.yaml
```

**View ingress:**

```bash
kubectl get ingress
```

**TLS example:**

```yaml
spec:
  tls:
  - hosts:
    - myapp.example.com
    secretName: tls-secret
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-service
            port:
              number: 80
```

---

## 20. StatefulSets

StatefulSets manage stateful applications such as databases. Unlike deployments, StatefulSets maintain stable network identities and persistent storage for each pod.

**StatefulSet example:**

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: postgres
  replicas: 3
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15
        ports:
        - containerPort: 5432
        volumeMounts:
        - name: data
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```

Each pod gets a unique name: `postgres-0`, `postgres-1`, `postgres-2`. Kubernetes creates a PVC for each pod automatically.

---

## 21. DaemonSets

DaemonSets ensure a copy of a pod runs on every node. They are useful for node-level services such as log collectors and monitoring agents.

**DaemonSet example:**

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
      - name: node-exporter
        image: prom/node-exporter
        ports:
        - containerPort: 9100
```

**Apply the DaemonSet:**

```bash
kubectl apply -f daemonset.yaml
```

Every node in the cluster runs a `node-exporter` pod.

---

## 22. Jobs and CronJobs

### Jobs

Jobs run tasks to completion. They are useful for batch processing and one-off tasks.

**Job example:**

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: data-import
spec:
  template:
    spec:
      containers:
      - name: importer
        image: my-importer:1.0
        command: ["python", "import.py"]
      restartPolicy: Never
  backoffLimit: 3
```

**Run the job:**

```bash
kubectl apply -f job.yaml
```

**View jobs:**

```bash
kubectl get jobs
```

**View job logs:**

```bash
kubectl logs job/data-import
```

### CronJobs

CronJobs run jobs on a schedule.

**CronJob example:**

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: my-backup:1.0
            command: ["sh", "backup.sh"]
          restartPolicy: OnFailure
```

This runs a backup every day at 02:00.

**Apply the CronJob:**

```bash
kubectl apply -f cronjob.yaml
```

**View CronJobs:**

```bash
kubectl get cronjobs
```

---

## 23. Practical Example: Full Application Stack

This example deploys a web application with a database and cache.

**postgres.yaml:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres
spec:
  selector:
    app: postgres
  ports:
  - port: 5432
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: postgres
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15
        env:
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        - name: POSTGRES_DB
          value: myapp
        ports:
        - containerPort: 5432
        volumeMounts:
        - name: data
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```

**redis.yaml:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis
spec:
  selector:
    app: redis
  ports:
  - port: 6379
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:alpine
        ports:
        - containerPort: 6379
```

**app.yaml:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 5000
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web
        image: my-app:1.0
        env:
        - name: DATABASE_URL
          value: postgres://postgres:5432/myapp
        - name: REDIS_URL
          value: redis://redis:6379
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        ports:
        - containerPort: 5000
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 5000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 5000
          initialDelaySeconds: 5
          periodSeconds: 5
```

**secret.yaml:**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: c2VjcmV0MTIz
```

**Deploy everything:**

```bash
kubectl apply -f secret.yaml
kubectl apply -f postgres.yaml
kubectl apply -f redis.yaml
kubectl apply -f app.yaml
```

**Check the deployment:**

```bash
kubectl get all
```

**Access the application:**

```bash
kubectl get service web
```

Use the external IP to access the application in your browser.

---

## 24. Monitoring and Debugging

### View Events

```bash
kubectl get events
```

### Describe Resources

```bash
kubectl describe pod <pod-name>
kubectl describe deployment <deployment-name>
kubectl describe service <service-name>
```

### View Logs

```bash
kubectl logs <pod-name>
kubectl logs <pod-name> -c <container-name>
kubectl logs -f <pod-name>
kubectl logs --previous <pod-name>
```

### Execute Commands

```bash
kubectl exec -it <pod-name> -- bash
kubectl exec <pod-name> -- ls /app
```

### Port Forwarding

Forward a local port to a pod:

```bash
kubectl port-forward pod/<pod-name> 8080:80
```

Forward to a service:

```bash
kubectl port-forward service/<service-name> 8080:80
```

### Copy Files

```bash
kubectl cp <pod-name>:/path/to/file ./local-file
kubectl cp ./local-file <pod-name>:/path/to/file
```

### Resource Usage

```bash
kubectl top nodes
kubectl top pods
```

Requires the metrics server to be installed.

---

## 25. Cleaning Up Resources

**Delete specific resources:**

```bash
kubectl delete pod <pod-name>
kubectl delete deployment <deployment-name>
kubectl delete service <service-name>
```

**Delete from file:**

```bash
kubectl delete -f deployment.yaml
```

**Delete all resources in a namespace:**

```bash
kubectl delete all --all -n dev
```

**Delete a namespace and everything in it:**

```bash
kubectl delete namespace dev
```

**Force delete stuck resources:**

```bash
kubectl delete pod <pod-name> --force --grace-period=0
```

---

## 26. Context and Configuration

kubectl uses contexts to connect to different clusters.

**View current context:**

```bash
kubectl config current-context
```

**List all contexts:**

```bash
kubectl config get-contexts
```

**Switch context:**

```bash
kubectl config use-context minikube
```

**View configuration:**

```bash
kubectl config view
```

**Set default namespace:**

```bash
kubectl config set-context --current --namespace=dev
```

---

## 27. Helm

Helm is a package manager for Kubernetes. It simplifies installing and managing applications.

**Install Helm:**

Follow the instructions at [helm.sh](https://helm.sh/docs/intro/install/)

**Add a chart repository:**

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

**Install an application:**

```bash
helm install my-postgres bitnami/postgresql
```

**List installed charts:**

```bash
helm list
```

**Upgrade a chart:**

```bash
helm upgrade my-postgres bitnami/postgresql --set auth.postgresPassword=newpassword
```

**Uninstall a chart:**

```bash
helm uninstall my-postgres
```

**Search for charts:**

```bash
helm search repo postgres
```

Helm charts simplify complex deployments by packaging multiple Kubernetes resources together.

---

## 28. Best Practices

### Resource Management

- Always set resource requests and limits
- Use horizontal pod autoscaling for variable workloads
- Monitor resource usage regularly
- Use namespace quotas to prevent resource exhaustion

### Configuration

- Store configuration in ConfigMaps
- Store secrets in Secret resources
- Never commit secrets to version control
- Use external secret management tools for production

### Deployment

- Use declarative YAML files instead of imperative commands
- Store all manifests in version control
- Use labels consistently for organisation
- Implement health checks for all applications
- Use rolling updates for zero-downtime deployments

### Networking

- Use services to abstract pod locations
- Implement network policies to restrict traffic
- Use ingress for HTTP/HTTPS routing
- Consider service mesh for complex networking requirements

### Storage

- Use StatefulSets for stateful applications
- Implement backup strategies for persistent volumes
- Use storage classes appropriate for workload requirements
- Clean up unused PVCs regularly

### Security

- Run containers as non-root users
- Use security contexts to restrict capabilities
- Implement pod security policies or pod security standards
- Scan images for vulnerabilities
- Rotate secrets regularly
- Use RBAC to control access

### Monitoring

- Implement logging aggregation
- Use metrics for monitoring and alerting
- Set up alerts for critical failures
- Monitor resource usage trends
- Keep audit logs enabled

### Organisation

- Use namespaces to separate environments
- Implement naming conventions
- Document custom resources and configurations
- Use GitOps workflows for deployment
- Regularly review and update dependencies

---

## 29. Kubernetes in CI/CD Pipelines

Kubernetes integrates with CI/CD tools to automate deployments.

**GitHub Actions example:**

```yaml
name: Deploy to Kubernetes

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Configure kubectl
        uses: azure/k8s-set-context@v3
        with:
          kubeconfig: ${{ secrets.KUBE_CONFIG }}
      
      - name: Deploy application
        run: |
          kubectl apply -f k8s/
          kubectl rollout status deployment/my-app
```

**Jenkins pipeline example:**

```groovy
pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                script {
                    sh 'kubectl apply -f k8s/'
                    sh 'kubectl rollout status deployment/my-app'
                }
            }
        }
    }
}
```

**GitLab CI example:**

```yaml
deploy:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl config use-context my-cluster
    - kubectl apply -f k8s/
    - kubectl rollout status deployment/my-app
  only:
    - main
```

---

## 30. Common Troubleshooting Commands

**Pod stuck in Pending:**

```bash
kubectl describe pod <pod-name>
```

Check events for scheduling issues, resource constraints or volume problems.

**Pod stuck in CrashLoopBackOff:**

```bash
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
```

The application is crashing on startup. Check logs for errors.

**Service not accessible:**

```bash
kubectl get endpoints <service-name>
kubectl describe service <service-name>
```

Verify the service selector matches pod labels.

**Image pull errors:**

```bash
kubectl describe pod <pod-name>
```

Check image name, tag and registry credentials.

**Connection refused errors:**

```bash
kubectl exec -it <pod-name> -- netstat -tuln
```

Verify the application is listening on the expected port.

**DNS resolution issues:**

```bash
kubectl exec -it <pod-name> -- nslookup kubernetes.default
```

Check CoreDNS pods are running:

```bash
kubectl get pods -n kube-system -l k8s-app=kube-dns
```

---

## 31. Next Steps

After mastering the basics, explore these advanced topics:

- **Service Mesh**: Istio, Linkerd for advanced networking and observability
- **Operators**: Custom controllers for managing complex applications
- **Custom Resource Definitions**: Extend Kubernetes with custom resources
- **Multi-cluster Management**: Tools like Rancher, Argo CD
- **Security**: OPA, Falco, network policies
- **Observability**: Prometheus, Grafana, Jaeger
- **GitOps**: Flux, Argo CD for declarative deployments
- **Serverless**: Knative for serverless workloads on Kubernetes

---

## Key Takeaway

Kubernetes provides a powerful platform for deploying and managing containerised applications at scale. Understanding pods, deployments, services and persistent storage gives you the foundation to run production workloads. Kubernetes handles the operational complexity of distributed systems, allowing engineers to focus on building applications. Start with local development using Minikube or Kind, then move to managed services as your requirements grow.

---