## Dynamic provisioning of persistent volumes
This is a feature native to kubernetes where the cluster can dynamically provision a volume if the users request of PVC doesn't match any pre created persistent volume. For more information about dynmic provisioning please refer to kubernetes documentation.[https://kubernetes.io/docs/concepts/storage/persistent-volumes/#dynamic]

## Storage Classes
To Allow dynamic provisioning of volumes, administrator must create storage classes that defines the classes of storage a user can provision volumes from. For more information about the storage classes, please refer to the kubernetes documentation[https://kubernetes.io/docs/concepts/storage/storage-classes/]

On deploying of Gluster Container Storage in the kbernetes cluster, there will be 2 storage classes created by default:

```
$ kubectl get storageclass
NAME                      PROVISIONER                    AGE
glusterfs-csi (default)   org.gluster.glusterfs          70m
glustervirtblock-csi      org.gluster.glustervirtblock   69m
local-storage             kubernetes.io/no-provisioner   80m
```
#### glusterfs-csi Storage Class

##### Parameters and default values
	Thin arbiter setup-> why use this as compared to replica 3

#### glustervirtblock-csi Storage Class

##### Parameters and default values

#### Create your own Storage Class

## Create RWO PersistentVolumeClaim
- Specify storage classes?

## Create RWX PersistentVolumeClaim

## Create ROX PersistentVolumeClaim
