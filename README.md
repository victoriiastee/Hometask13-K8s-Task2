# Hometask13-K8s-Task2

## Execution: 

### Task 1 

* Create an instance on which you will install kubernetes using kubespray: 

* Test SSH connection from local machine: 

![image](https://user-images.githubusercontent.com/102364456/218344297-15a5610d-7bb6-4c32-8014-ac157735afa0.png)

* Clone Kubespray release  repository: 

```
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray
git checkout release-2.20
```
* Copy and edit inventory file:
```
cp -rfp inventory/sample inventory/mycluster
```
```
nano inventory/mycluster/inventory.ini
```
>>>
```
[all]
node1 ansible_host=VM_publick_ip

[kube_control_plane]
node1

[etcd]
node1

[kube_node]
node1

[calico_rr]

[k8s_cluster:children]
kube_control_plane
kube_node
calico_rr
```
* Turn on MetalLB:
```
nano inventory/mycluster/group_vars/k8s_cluster/addons.yml
```
>>>
```
metallb_enabled: true
metallb_speaker_enabled: true
metallb_avoid_buggy_ips: true
metallb_ip_range:
  - "VM_private_ip/32"
```
```
nano inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml
```
>>>
```
kube_proxy_strict_arp: true
```
* Run execute container:
```
sudo docker run --rm -it -v ~/kubespray:/mnt/kubespray -v ~/.ssh:/pem quay.io/kubespray/kubespray:v2.20.0 bash
```
* Go to kubespray folder and start ansible-playbook (command in container):
```
cd /mnt/kubespray
```
```
ansible-playbook -i inventory/mycluster/inventory.ini --private-key /pem/id_rsa -e ansible_user=remote_user(your_username) -b cluster.yml
```
![image](https://user-images.githubusercontent.com/102364456/218342206-cfe3a042-2fe4-4598-954a-729ba5ec9360.png)

* After successful installation connect to VM (in container) and copy kubectl configuration file:
```
ssh -i /pem/id_rsa remote_user@YOUR_VM_IP
```
```
mkdir ~/.kube
sudo cp /etc/kubernetes/admin.conf ~/.kube/config
sudo chmod 777 ~/.kube/config
kubectl get nodes
```
As result, you will see installed node: 

```
kubectl get nodes
```
![image](https://user-images.githubusercontent.com/102364456/218341789-7f0fece1-1972-487b-9fb7-d9c1776f81ad.png) 

### Task 2



* Install Ingress-controller:
```
kubectl apply -f nginx-ctl.yaml
```
```
kubectl apply -f path_provisioner.yml
```

A list of pods:
```
kubectl get pods -n ingress-nginx -w
```
![image](https://user-images.githubusercontent.com/102364456/218342110-55f4ea16-b5ab-41e9-b5ed-a47f3609717a.png)

A list of services:
```
kubectl get svc --all-namespaces
```
![image](https://user-images.githubusercontent.com/102364456/218342253-f776c6cd-8ad3-492a-baee-576c4e188722.png)

### Task 3 


* Create domain name: 


You can use a free service to get a domain name https://dynv6.com/ 

![image](https://user-images.githubusercontent.com/102364456/218344435-46bab256-9b31-4f8f-87c7-2c00073de82d.png)

### Task 4 


* Run [nginx.yaml](nginx.yaml) file:
```
kubectl apply -f nginx.yaml
```

* Run [ingress.yaml](ingress.yaml) file:
```
kubectl apply -f ingress.yaml
```
```
kubectl get deploy
kubectl get svc 
kubectl get ingress
```
![image](https://user-images.githubusercontent.com/102364456/218342178-bf91e4ee-7972-46bf-9ce4-17c97a84b78a.png)

#### Certificate:

* Installing cert-manager:
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.yaml
```

* Run [letsencrypt-staging.yaml](letsencrypt-staging.yaml):
```
kubectl apply -f letsencrypt-staging.yaml
```
* Run [letsencrypt-production.yaml](letsencrypt-production.yaml):
```
kubectl apply -f letsencrypt-production.yaml
```
```
kubectl get certificate
```
![image](https://user-images.githubusercontent.com/102364456/218342079-73f0aa99-66be-43fa-83a3-a2c4e5accdfa.png)

### Result
URL: https://vicstee.dns.army//
![image](https://user-images.githubusercontent.com/102364456/218342235-4afce062-4381-419c-9b08-c4aad2d5e073.png)

#### Thank You!
