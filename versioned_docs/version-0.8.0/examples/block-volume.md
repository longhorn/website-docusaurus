---
title: Block Volume
sidebar_position: 1
---


```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: longhorn-block-vol
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Block
  storageClassName: longhorn
  resources:
    requests:
      storage: 2Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: block-volume-test
  namespace: default
spec:
  containers:
    - name: block-volume-test
      image: nginx:stable-alpine
      imagePullPolicy: IfNotPresent
      volumeDevices:
        - devicePath: /dev/longhorn/testblk
          name: block-vol
      ports:
        - containerPort: 80
  volumes:
    - name: block-vol
      persistentVolumeClaim:
        claimName: longhorn-block-vol
```