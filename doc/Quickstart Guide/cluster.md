The vagrant tool in the gcs repository can be used to bring up a small cluster with 3 nodes, with kubernetes and gluster container storage(GCS) setup. Please follow the below instructions to deploy the cluster:

1. Clone gcs repository
   ```
   $ git clone --recurse-submodules git@github.com:gluster/gcs.git
   ```

2. Setup the cluster
   ```
   $ cd gcs/deploy
   $ vagrant up
   ```

   If there are any timeout failures with GCS components in the deploy, please retry
   ``` 
   $ vagrant destroy -f
   $ vagrant up
   ```

3. Verify the cluster is up and running
   ``` 
   $ vagrant ssh kube1
   [vagrant@kube1 ~]$ kubectl get pods -ngcs
   NAME                                    READY   STATUS      RESTARTS   AGE
   alertmanager-alert-0                    2/2     Running     0          64m
   alertmanager-alert-1                    2/2     Running     0          64m
   anthill-58b9b9b6f-c8rhw                 1/1     Running     0          74m
   csi-glusterfsplugin-attacher-0          2/2     Running     0          68m
   csi-glusterfsplugin-nodeplugin-cvnzw    2/2     Running     0          68m
   csi-glusterfsplugin-nodeplugin-h9hf6    2/2     Running     0          68m
   csi-glusterfsplugin-nodeplugin-nhvr4    2/2     Running     0          68m
   csi-glusterfsplugin-provisioner-0       4/4     Running     0          68m
   csi-glustervirtblock-attacher-0         2/2     Running     0          66m
   csi-glustervirtblock-nodeplugin-8kdjt   2/2     Running     0          66m
   csi-glustervirtblock-nodeplugin-jwb77   2/2     Running     0          66m
   csi-glustervirtblock-nodeplugin-q5cnp   2/2     Running     0          66m
   csi-glustervirtblock-provisioner-0      3/3     Running     0          66m
   etcd-988db4s64f                         1/1     Running     0          73m
   etcd-gqds2t99bn                         1/1     Running     0          74m
   etcd-kbw8rpxfmr                         1/1     Running     0          74m
   etcd-operator-77bfcd6595-zvrdt          1/1     Running     0          75m
   gluster-kube1-0                         2/2     Running     1          73m
   gluster-kube2-0                         2/2     Running     1          72m
   gluster-kube3-0                         2/2     Running     1          72m
   gluster-mixins-62jkz                    0/1     Completed   0          64m
   grafana-9df95dfb5-tqrqw                 1/1     Running     0          64m
   kube-state-metrics-86bc74fd4c-mh6kl     4/4     Running     0          65m
   node-exporter-855rg                     2/2     Running     0          64m
   node-exporter-bxwg9                     2/2     Running     0          64m
   node-exporter-tm98k                     2/2     Running     0          65m
   prometheus-operator-6c4b6cfc76-lsgxb    1/1     Running     0          65m
   prometheus-prometheus-0                 3/3     Running     1          65m
   prometheus-prometheus-1                 3/3     Running     1          63m
   ```

4. Create your first PVC from the gluster container storage

   RWO PV:
   ```
   $ cat pvc.yaml
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: glusterblock-csi-pv
   spec:
     storageClassName: glustervirtblock-csi
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 100Mi
   ```

   RWX PV:
   ```
   $ cat pvc.yaml
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: glusterblock-csi-pv
   spec:
     storageClassName: glustervirtblock-csi
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 100Mi
   ```

   Note: This is a test cluster with very less resources allocated to the cluster nodes. Hence any kind of scale/stress test on this shall lead to failures and node not responding state. If you have to try scale/stress tests, please use the Deplyments guide[] to deploy GCS on nodes that meet the resource requirements.

5. Destroy the cluster
   From the node where vagrant up was executed, run the following command to destroy the kubernetes cluster
   ```
   $ cd gcs/deploy
   $ vagrant destroy -f
   ```
