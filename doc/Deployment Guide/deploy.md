To deploy Gluster Container Storage(GCS), we expect a kubernetes cluster. If you do not already have the kubernetes cluster, you can deploy kubernetes on Centos(guide)[https://github.com/kotreshhr/gcs-setup/tree/master/kube-cluster] , coreos[] and many others.

Once you have the kubernetes cluster up and running, its few simple steps to deploy GCS.

#### Deploy GCS on kubernetes Cluster

The python tool way:

https://github.com/kotreshhr/gcs-setup/tree/master/kube-cluster
https://github.com/kotreshhr/gcs-setup/tree/master/gcs-setup
Note: Make sure that the devices do not have file syetem, and are not part of any pv/vg/lvm, if not ensured the device addition step of gcs deployment will fail.

Or the Ansible way:

1. Install ansible on master node:<br/>
   `$ yum install ansible`

2. To ensure ansible is able to ssh into the nodes, add public key of master node{kube1} as authorized
   key in  other kube nodes<br/>
   `$  cat ~/.ssh/id_rsa.pub | ssh root@kube2 'cat >> ~/.ssh/authorized_keys'`

3. In the kubernetes master node, gluster the gcs repo:
  `$ git clone --recurse-submodules git@github.com:gluster/gcs.git`

4. Create an inventory file to be used by the GCS ansible playbook:
    ```
    $ cat ~/gcs/deploy/inventory.yml
    kube1 ansible_host=<node-ip> gcs_disks='["<device1>", "<device2>"]'
    kube2 ansible_host=<node-ip> gcs_disks='["<device1>", "<device2>"]'
    kube3 ansible_host=<node-ip> gcs_disks='["<device1>", "<device2>"]'

    ## Hosts that will be kubernetes master nodes
    [kube-master]
    kube1

    ## Hosts that will be kubernetes nodes
    [kube-node]
    kube1
    kube2
    kube3

    ## Hosts that will be used for GCS.
    ## Systems grouped here need to define 'gcs_disks' as hostvars, which are the disks that will be used by GCS to
    provision storage.
    [gcs-node]
    kube1
    kube2
    kube3
    ```

5. Deploy GCS on kubernetes cluster
   Execute the deploy-gcs.yml file using ansible from master node.
   `$ ansible-playbook -i <path to inventory file>/<name of inventory file>  <path to deploy-gcs.yml file>/deploy-gcs.yml`.
   eg: `$ cd ~/gcs ; ansible-playbook -i deploy/inventory.yml deploy/deploy-gcs.yml`

6. Verify the deployment is successfull
   `$ kubectl get pods -n gcs`
   All the pods should be in `Running` state.

