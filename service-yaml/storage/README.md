# storage 验证

1. 如果创建好pvc后，没有引用到pod中，会有
   > 2s          Normal   WaitForFirstConsumer   persistentvolumeclaim/elasticsearch-pvc   waiting for first consumer to be created before binding
   
这类的events发生。

2. 在pod中会声明pvc，如果pvc没有找到对应的pv，会让对应的storageClass来进行动态的pv创建（sc的pv创建是通过provisioner deploy的动态创建实现的）。
   > 0s          Normal   ExternalProvisioning   persistentvolumeclaim/busybox-pvc         waiting for a volume to be created, either by external provisioner "nfs-nfs" or manually created by system administrator
   
提示说的是，等待pv创建，可以通过provisioner自动创建或者系统管理员手动创建。


在k8s中， 当创建pvc的时候，梳理下pv、pvc、storageclass、provisioner这几个组件分别做了什么？链路是怎样的？以下是gpt的回答：
```
Hi! In Kubernetes, the components PV (Persistent Volume), PVC (Persistent Volume Claim), StorageClass, and Provisioner play different roles in the storage management process.

1. Persistent Volume (PV): PV represents a piece of storage in the cluster. It can be a physical disk, a network storage device, or a cloud storage resource. PVs are provisioned by the cluster administrator and can be dynamically or statically provisioned.

2. Persistent Volume Claim (PVC): PVC is a request for storage by a user or a pod. It is used to claim a specific amount of storage from a PV. PVCs are created by users and are bound to a PV that satisfies the requested storage requirements.

3. StorageClass: StorageClass is an object that defines the provisioner and parameters for dynamically provisioning PVs. It acts as an abstraction layer between users and the underlying storage infrastructure. When a PVC is created without specifying a PV, the StorageClass is used to dynamically provision a suitable PV.

4. Provisioner: A provisioner is responsible for creating and managing PVs. It interacts with the underlying storage infrastructure to provision the requested storage. Different provisioners support different storage technologies, such as local storage, NFS, or cloud storage providers.

The chain of events when creating a PVC is as follows:

1. The user creates a PVC object, specifying the desired storage capacity and other parameters.

2. The PVC controller watches for new PVC objects and checks if a suitable PV is available.

3. If a suitable PV is found, the PVC is bound to the PV, and the user can use the PVC to mount the storage in their pods.

4. If a suitable PV is not available, the StorageClass associated with the PVC is used to dynamically provision a new PV.

5. The provisioner creates a new PV based on the StorageClass definition and binds it to the PVC.

6. Once the PVC is bound to a PV, the user can use the PVC to mount the storage in their pods.

I hope this clarifies the roles and the chain of events for these components in Kubernetes storage management. Let me know if you have any more questions!
```