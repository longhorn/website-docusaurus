---
title: CSI Persistent Volume
sidebar_position: 100
---


```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: longhorn-vol-pv
spec:
  capacity:
    storage: 2Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: longhorn
  csi:
    driver: driver.longhorn.io
    fsType: ext4
    volumeAttributes:
      numberOfReplicas: '3'
      staleReplicaTimeout: '2880'
    volumeHandle: existing-longhorn-volume
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: longhorn-vol-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
  volumeName: longhorn-vol-pv
  storageClassName: longhorn
---
apiVersion: v1
kind: Pod
metadata:
  name: volume-pv-test
  namespace: default
spec:
  restartPolicy: Always
  containers:
  - name: volume-pv-test
    image: nginx:stable-alpine
    imagePullPolicy: IfNotPresent
    livenessProbe:
      exec:
        command:
          - ls
          - /data/lost+found
      initialDelaySeconds: 5
      periodSeconds: 5
    volumeMounts:
    - name: vol
      mountPath: /data
    ports:
    - containerPort: 80
  volumes:
  - name: vol
    persistentVolumeClaim:
      claimName: longhorn-vol-pvc
```