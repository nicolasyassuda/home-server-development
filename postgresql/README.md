# Deployment of POSTGRESQL
I recommend you to create a namespace called *database* to your enviroment be more organized.

*database-namespace.yaml*
```
apiVersion: v1
kind: Namespace
metadata:
  name: database
  labels:
    name: database
```
*postgre-configmap.yaml*
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: postgres-secret
  labels:
    app: postgres
data:
  POSTGRES_DB: ps_db
  POSTGRES_USER: admin
  POSTGRES_PASSWORD: MostSecurePass@123
```
Here has a very important step if you using *microk8s* you need to create a StorageClass to map the new path:
The explanation to why you need to create this is here (https://microk8s.io/docs/addon-hostpath-storage).

*psql-sc.yaml*
```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: psql-hostpath
provisioner: microk8s.io/hostpath
reclaimPolicy: Delete
parameters:
  pvDir: <YOUR_PATH_IN_HOST_MACHINE>
volumeBindingMode: WaitForFirstConsumer
```
If you not using you can use the next step.

In this step you need to pay attention if your user from K8s can acess this *<YOUR_PATH_IN_HOST_MACHINE>*

*psql-pv.yaml*
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: postgres-volume
  labels:
    type: local
    app: postgres
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: <YOUR_PATH_IN_HOST_MACHINE>
```
The claim is responsable to bind the Persistent Volume to be used.
If you create the StorageClass you need to reference here:

*psql-claim.yaml*
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-volume-claim
  namespace: database
spec:
  storageClassName: psql-hostpath #  PUT the name of storageclass
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
```

Or if you dont using StorageClass you can use this one:

*psql-claim.yaml*
```
piVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-volume-claim
  namespace: database # Same here
  labels:
    app: postgres
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
```

*ps-deployment.yaml*
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: database
spec:
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
          image: 'postgres:17'
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 5432
          envFrom:
            - configMapRef:
                name: postgres-secret
          volumeMounts:
            - mountPath: /var/lib/postgresql/data
              name: postgresdata
      volumes:
        - name: postgresdata
          persistentVolumeClaim:
            claimName: postgres-volume-claim
```
