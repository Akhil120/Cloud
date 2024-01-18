# Kubernetes Cheat sheet

### Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
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
      - name: my-container
        image: nginx:latest
        ports:
        - containerPort: 80
```

- **`apiVersion`:** Specifies the API version for the resource (`apps/v1` for Deployment).
- **`kind`:** Defines the type of Kubernetes resource (Deployment in this case).
- **`metadata`:** Contains metadata for the resource, such as the name of the Deployment (`my-deployment`).
- **`spec`:** Describes the desired state for the resource.
  - **`replicas`:** Specifies the desired number of replicas (Pods) to run (3 in this case).
  - **`selector`:** Defines how the Deployment identifies which Pods to manage.
    - **`matchLabels`:** Specifies labels that Pods must have to be selected by this Deployment.
  - **`template`:** Defines the Pod template for the Deployment.
    - **`metadata`:** Labels to apply to Pods created from this template.
    - **`spec`:** Defines the Pod's specification.
      - **`containers`:** Lists the containers within the Pod.
        - **`name`:** Name of the container (`my-container`).
        - **`image`:** Docker image for the container (`nginx:latest`).
        - **`ports`:** Lists the ports that should be exposed in the container.

### Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

- **`apiVersion`:** API version for the Service (`v1` for Service).
- **`kind`:** Type of resource (Service).
- **`metadata`:** Metadata for the Service (name is `my-service`).
- **`spec`:** Describes the desired state for the Service.
  - **`selector`:** Labels used to select Pods that the Service will route traffic to.
    - **`app`:** Pods must have this label (`my-app`) to be selected.
  - **`ports`:** List of ports that the Service should expose.
    - **`protocol`:** Protocol for the port (TCP).
    - **`port`:** Port on the Service.
    - **`targetPort`:** Port on the Pod.
  - **`type`:** Type of Service (`ClusterIP` means the service is only accessible within the cluster).

### ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  key1: value1
  key2: value2
```

- **`apiVersion`:** API version for the ConfigMap (`v1` for ConfigMap).
- **`kind`:** Type of resource (ConfigMap).
- **`metadata`:** Metadata for the ConfigMap (name is `my-configmap`).
- **`data`:** Key-value pairs containing configuration data.

### Secret:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: dXNlcm5hbWU=
  password: cGFzc3dvcmQ=
```

- **`apiVersion`:** API version for the Secret (`v1` for Secret).
- **`kind`:** Type of resource (Secret).
- **`metadata`:** Metadata for the Secret (name is `my-secret`).
- **`type`:** Type of the Secret (`Opaque` means arbitrary data). Other types include `kubernetes.io/service-account-token`.
- **`data`:** Base64-encoded key-value pairs containing sensitive data.

### PersistentVolume:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
```

- **`apiVersion`:** API version for the PersistentVolume (`v1` for PersistentVolume).
- **`kind`:** Type of resource (PersistentVolume).
- **`metadata`:** Metadata for the PersistentVolume (name is `my-pv`).
- **`spec`:** Describes the desired state for the PersistentVolume.
  - **`capacity`:** Storage capacity of the volume (`1Gi`).
  - **`accessModes`:** Access modes for the volume (`ReadWriteOnce` means it can be mounted as read-write by a single node).
  - **`hostPath`:** Specifies that the volume is backed by a directory on the node (`/mnt/data`).

### PersistentVolumeClaim:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

- **`apiVersion`:** API version for the PersistentVolume

Claim (`v1` for PersistentVolumeClaim).
- **`kind`:** Type of resource (PersistentVolumeClaim).
- **`metadata`:** Metadata for the PersistentVolumeClaim (name is `my-pvc`).
- **`spec`:** Describes the desired state for the PersistentVolumeClaim.
  - **`accessModes`:** Desired access modes for the volume (`ReadWriteOnce`).
  - **`resources`:** Specifies the desired resources for the claim.
    - **`requests`:** Requests storage (`1Gi`).

### StatefulSet:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-statefulset
spec:
  replicas: 3
  serviceName: my-statefulset
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:latest
        ports:
        - containerPort: 80
  volumeClaimTemplates:
  - metadata:
      name: my-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi
```

- **`apiVersion`:** API version for the StatefulSet (`apps/v1` for StatefulSet).
- **`kind`:** Type of resource (StatefulSet).
- **`metadata`:** Metadata for the StatefulSet (name is `my-statefulset`).
- **`spec`:** Describes the desired state for the StatefulSet.
  - **`replicas`:** Specifies the desired number of replicas (3 in this case).
  - **`serviceName`:** Specifies the name of the headless service associated with the StatefulSet.
  - **`selector`:** Labels used to identify Pods managed by the StatefulSet.
  - **`template`:** Defines the Pod template for the StatefulSet.
    - **`metadata`:** Labels to apply to Pods created from this template.
    - **`spec`:** Defines the Pod's specification.
      - **`containers`:** Lists the containers within the Pod.
        - **`name`:** Name of the container (`my-container`).
        - **`image`:** Docker image for the container (`nginx:latest`).
        - **`ports`:** Lists the ports that should be exposed in the container.
  - **`volumeClaimTemplates`:** Specifies templates for PersistentVolumeClaims that Pods in the StatefulSet will use.
    - **`metadata`:** Metadata for the PersistentVolumeClaim template.
      - **`name`:** Name of the PersistentVolumeClaim template (`my-pvc`).
    - **`spec`:** Describes the desired state for the PersistentVolumeClaim template.
      - **`accessModes`:** Desired access modes for the volume (`ReadWriteOnce`).
      - **`resources`:** Specifies the desired resources for the claim.
        - **`requests`:** Requests storage (`1Gi`).

### Ingress:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: my-app.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
```

- **`apiVersion`:** API version for the Ingress (`networking.k8s.io/v1` for Ingress).
- **`kind`:** Type of resource (Ingress).
- **`metadata`:** Metadata for the Ingress (name is `my-ingress`).
- **`spec`:** Describes the desired state for the Ingress.
  - **`rules`:** List of host rules for the Ingress.
    - **`host`:** Hostname for the rule (`my-app.com`).
    - **`http`:** Defines HTTP-specific configurations.
      - **`paths`:** List of paths to route to different services.
        - **`path`:** Path for the rule (`/`).
        - **`pathType`:** Type of path (`Prefix` means match the URL prefix).
        - **`backend`:** Specifies the backend service.
          - **`service`:** Specifies the name of the service (`my-service`).
          - **`port`:** Specifies the port on the service (`80`).

# Sample YAML Reference 
```yaml
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
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
      - name: my-container
        image: nginx:latest
        ports:
        - containerPort: 80

# Service
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP

# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  key1: value1
  key2: value2

# Secret
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: dXNlcm5hbWU=
  password: cGFzc3dvcmQ=

# PersistentVolume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data

# PersistentVolumeClaim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

# StatefulSet
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-statefulset
spec:
  replicas: 3
  serviceName: my-statefulset
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:latest
        ports:
        - containerPort: 80
  volumeClaimTemplates:
  - metadata:
      name: my-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi

# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: my-app.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80

```

