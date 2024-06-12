# Aerospike-local-deployment

# 1. Create a namespace

Apply the given aerospike namespace yaml file

    kubectl apply -f aerospike-namespace.yaml

# 2. Prerequisites

> 1. make sure when you create PVs the directory/folder must present on your machine
> 2. When you create a PV the deafult volumeMode is **Filesystem**. if you are using EBS storage then the it is **Block**

        storageClassName: aerospike-local
        accessModes: ["ReadWriteOnce"]
        volumeMode: Filesystem
        resources:
          requests:
            storage: 5Gi
