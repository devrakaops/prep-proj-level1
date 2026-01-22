# ARCHITECTURE (Phase-1)

```
WordPress (Deployment)  ──►  MySQL (StatefulSet)
        │                        │
        │                        └─ PV (/mnt/mysql)
        │
        └─ PV (/mnt/wordpress)  [wp-content]
```

* MySQL = **Stateful**
* WordPress code = **Stateless**
* WordPress uploads/themes/plugins = **Persistent**
* DB access = **ClusterIP (internal only)**

---

# 📁 FILE-1: MySQL Secret

📄 `mysql-secret.yaml`

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  MYSQL_ROOT_PASSWORD: cm9vdA==   # root
```

> 🔹 Change password → base64 encode first

---

# 📁 FILE-2: MySQL PersistentVolume

📄 `mysql-pv.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/mysql
```

---

# 📁 FILE-3: MySQL StatefulSet (CORRECT & CLEAN)

📄 `mysql-statefulset.yaml`

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_DATABASE
          value: wordpress
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: mysql-storage
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 5Gi
```

---

# 📁 FILE-4: MySQL Service (ClusterIP)

📄 `mysql-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  type: ClusterIP
  selector:
    app: mysql
  ports:
  - port: 3306
    targetPort: 3306
```

---

# 📁 FILE-5: WordPress PersistentVolume

📄 `wordpress-pv.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: wordpress-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/wordpress
```

---

# 📁 FILE-6: WordPress PersistentVolumeClaim

📄 `wordpress-pvc.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wordpress-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

---

# 📁 FILE-7: WordPress Deployment (REFINED & CORRECT)

📄 `wordpress-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
      - name: wordpress
        image: wordpress:latest
        ports:
        - containerPort: 80
        env:
        - name: WORDPRESS_DB_HOST
          value: mysql
        - name: WORDPRESS_DB_NAME
          value: wordpress
        - name: WORDPRESS_DB_USER
          value: root
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_ROOT_PASSWORD
        volumeMounts:
        - name: wordpress-storage
          mountPath: /var/www/html/wp-content
      volumes:
      - name: wordpress-storage
        persistentVolumeClaim:
          claimName: wordpress-pvc
```

---

# 📁 FILE-8: WordPress Service (NodePort)

📄 `wordpress-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: wordpress
spec:
  type: NodePort
  selector:
    app: wordpress
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30007
```

---

# 🚀 APPLY ORDER (IMPORTANT)

```bash
kubectl apply -f mysql-secret.yaml
kubectl apply -f mysql-pv.yaml
kubectl apply -f mysql-statefulset.yaml
kubectl apply -f mysql-service.yaml

kubectl apply -f wordpress-pv.yaml
kubectl apply -f wordpress-pvc.yaml
kubectl apply -f wordpress-deployment.yaml
kubectl apply -f wordpress-service.yaml
```

---

# ✅ FINAL VERIFICATION CHECKLIST

### Pods

```bash
kubectl get pod
```


---

### Storage

```bash
kubectl get pv,pvc
```

---

### MySQL

```bash
kubectl logs mysql-0 | tail
kubectl exec -it mysql-0 -- mysql -uroot -p
```

---

### WordPress

Open:

```
http://<NodeIP>:30007
```

---

# 🧪 MUST-DO TESTS

### 🔹 Stateless proof

```bash
kubectl delete pod -l app=wordpress
```

➡️ Site should available and do still works

---

### 🔹 Stateful proof

```bash
kubectl delete pod mysql-0
```

➡️ Your Data is still there

---

### 🔹 WordPress storage proof

* Upload image / theme
* Delete WordPress pod
* Image/theme still exists

---

. Now we can deploy the same application using ARGOCD "GitOps" approach. Which i am going to tell you in next phase.
. Now we are going to deploy same application using ArgoCD-Deployment that is a GitOps Continious Deployment of the application.
Go to this link for this: 
