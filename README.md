# Aerospike-local-deployment

# 1. Create a namespace

Apply the given aerospike namespace yaml file

    kubectl apply -f aerospike-namespace.yaml

# 2. Instructions

> 1.  make sure when you create PVs the directory OR folder must present on your machine

        local:
          path: /opt/aerospike-0

> 2.  When you create a PV the deafult volumeMode is **Filesystem**. if you are using EBS storage then it is **Block**

        storageClassName: aerospike-local
        accessModes: ["ReadWriteOnce"]
        volumeMode: Filesystem
        resources:
          requests:
            storage: 5Gi

> 3.  Make sure you specify the correct name of Node in nodeAffinity -> values of PVs.

> > - **RUN** --> **_kubectl get nodes_** and replace with -> node01

        nodeAffinity:
          required:
          nodeSelectorTerms:
              - matchExpressions:
                  - key: kubernetes.io/hostname
                  operator: In
                  values:
                      - node01

> 4. Make sure the **_path & file_** mentioned in storage-engine device must present in you machine

- aerospike-config.yaml -> namespace store{ storage-engine device {file /opt/aero/data.dat} }

**ConfigMap**

        namespace store {
          replication-factor 3
          memory-size 1G
          default-ttl 30d
          nsup-period 120
          storage-engine device {
            file /opt/aero/data.dat
            filesize 1G
            write-block-size 128K
          }
        }

**SatefulSet**

        volumeMounts:
        - name: aerospike-pd
            mountPath: /opt/aero

# 3. apply the deployment file

> - ## Persistent Volume

> > 1.  create a `persistent volume` file and mention all the PVs into that.
> > 2.  give the right name of node in **nodeAffinity** of PV
> > 3.  make sure the **name** & **Path** of each PVs must be unique.

        kubectl apply -f aerospike-pv.yaml

> - ## Storage class

> > 1.  create `storage-class` & apply for local volume with **kubernetes.io/no-provisioner**

        kubectl apply -f aerospike-sc.yaml

> - ## ConfigMap

> > 1. create & apply `configMap` ( **make sure you read instructions first** )

        kubectl apply -f aerospike-cm.ymal

> - ## Service

> > 1. create & apply `services`
> >
> > - one service for internal communication
> > - one for external communication

        kubectl apply -f aerospike-svc.yaml

> - ## StatefulSet

> > 1.  create & apply `StatefulSet` ( **make sure you read instructions first** )

        kubectl apply -f aerospike-ss.yaml

# 4. Single complete yaml file for aerospike deployment

        kubectl apply -f aerospike-complete.yaml

# 5. Testing

> - ## aerospike tool installation

> > 1.  create & deploy aerospike-tool pod

        kubectl apply -f aerospike-tool.yaml

> > 2.  Enter into aerospike-tool pod

        kubectl exec -it aerospike-0 -n aerospike -- bash

> > 3.  connect to aerospike-pod using host-ip.

        asadm -h aerospike-svc.aerospike
