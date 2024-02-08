# kubernetes-cluster-setup-ansible

Ansible playbooks for deploying a 3 node Kubernetes cluster with 1 master node and 2 worker nodes. The default user 
that will be used is `k8s` so this user must exist on your servers (with sudo permissions).

# Ansible setup

Your local machine will need to have Ansible installed. This can be done with the following command:

For MAC
```
brew install ansible
```

For Linux/WSL
```
apt install ansible
```

# Pre-requisites
You need to generate rsa keys on your local machine and copy the rsa.pub to manage nodes. The can be done with following command:

Generate rsa keys

```
ssh-keygen
```
Copy rsa_pub to managed nodes
```
ssh-copy-id -i ~/.ssh/id_rsa.pub k8s@master
ssh-copy-id -i ~/.ssh/id_rsa.pub k8s@worker1
ssh-copy-id -i ~/.ssh/id_rsa.pub k8s@worker2
```

Installation of Kubernetes requires the ability to run commands as root, you will need to be able to 
sudo on the servers without a password. 

This can be done by running the following command on each of the servers:

```
echo "<username> ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/<username>
```

# Pre-checking ansible
1. Mapping IP address and Name in the `hosts` of your local machine.

2. You need to modified the `inventory.ini` with your master and workers information.

```
[master]
k8s@k8s-master

[worker]
k8s@k8s-worker1
k8s@k8s-worker2
```
3. You can then test that Ansible can connect to the servers with the following command:

```
ansible -i hosts all -m ping
```

# Running the playbooks

The playbooks can be run with the following command:

```
ansible-playbook -i inventory.ini formatted-clustersetup.yaml
```

Once the playbook has completed you should have a fully functional Kubernetes cluster!

# Known Issue

If initializing the master node is taking too long, You need to login to master node and check the tail -f /var/log/syslog. There you will see the "err="failed to load kubelet config file, path: /var/lib/kubelet/config.yaml".

As as workaround, Please type the following command in the master node:

```
kubeadm init
```

*Note - You need to type before getting the factal error in ansible-playbook job*

# Optional services

MetalLB and longhorn

