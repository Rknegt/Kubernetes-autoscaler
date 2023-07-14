# Kubernetes Edge-Cloud system including Horizontal Pod Autoscaler (HPA) setup using Ansible

## Installation
First of all, the hosts file needs to be changed for your specific setup.

To make things easier we will be setting up a passwordless sudo user named ubuntu. It depends a little how you set up the ssh connection, if you set up ssh with the user `initial_user` with public key you would do it with the command below. For password login just specify the `-k` parameter.

```sh
ansible-playbook -K -i kube-cluster/hosts.yml kube-cluster/setup_users.yml -e "ansible_user=initial_user"
```
If you want to update all the VMs and reboot afterwards, run this command:
```sh
ansible-playbook -i kube-cluster/hosts.yml kube-cluster/update_all.yml
```
Afterwards, install Kubernetes with its dependencies. The command below will install the latest version of Kubernetes with its depencies using `kubeadm`. Moreover, it will install the latest version of `containerd` as container runtime.
```sh
ansible-playbook -i kube-cluster/hosts.yml kube-cluster/install_k8s.yml
```
To actually set up the cluster we run the `setup_cluster` playbook. The variables of the "Set node labels" task need to be customized to reflect the actual hostnames in your setup.
```sh
ansible-playbook -i kube-cluster/hosts.yml kube-cluster/setup_cluster.yml
```
When the cluster is up and running, which can be checked with `kubectl get nodes`, we want to deploy our demo application with helm:
```sh
ansible-playbook -i kube-cluster/hosts.yml kube-cluster/deploy_helm.yml
```
With our demo application running, we can start the metrics-server and the HPA with the `setup_hpa` playbook:
```sh
ansible-playbook -i kube-cluster/hosts.yml kube-cluster/setup_hpa.yml
```
Now the edge-cloud autoscaler setup is running and you can test out your application to see if it works

## Our experimental setup
- Kubernetes (kubeadm) 1.27.2
- Ubuntu 22.04 VMs
- Digital Ocean as cloud provider
- Containerd 1.6.21
- Flannel 0.22.0
- Google Online Boutique 0.8.0
- Ansible 2.15.0
- Helm 3.10.1
- Kubernetes Metrics-server 0.6.3

## License
Apache License 2.0 https://www.apache.org/licenses/LICENSE-2.0

## Authors
[Rick Knegt](https://github.com/rknegt) & [Stef Sier](https://github.com/stefsier) (https://os3.nl students)

## Disclaimer
This work is published by students of the SNE (OS3) Master for their research project.

## Helpful resources we used
- https://github.com/torgeirl/kubernetes-playbooks
- https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-20-04
- https://nvtienanh.info/en/devops-en/install-kubernetes-cluster-on-ubuntu-22-04-20230223
- https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
- https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
