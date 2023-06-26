# Prepare VM

## 1. create VM & clone

- Create VM using ubuntu iso image and complete steps 2 - 5, then create clone

- (Clone) change ip address

  ```
  vim /etc/netplan/00-installer-config.yaml
  
  # This is the network config written by 'subiquity'
  network:
    ethernets:
      enp0s5:
        addresses:
        - 192.168.0.101/24
        routes:
          - to: default
            via: 192.168.0.2
        nameservers:
          addresses:
          - 192.168.0.2
          search: []
    version: 2
  
  netplan apply
  ```

- (Clone) change hostname

  ```shell
  vim /etc/hostname
  reboot
  
  (optional) vim /etc/hosts
  ```

- (Clone) change uuid

  ```
  prlctl unregister <uuid>
  prlctl register <path> --regenerate-src-uuid
  
  cat /sys/class/dmi/id/product_uuid
  ```

## 2. /etc/sysctl.d/k8s.conf

```shell
cat > /etc/sysctl.d/k8s.conf << EOF
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
vm.swappiness = 0
EOF
```

## 3. swapoff

```shell
swapoff -a
sed -ir 's/.*swap/#&/g' /etc/fstab

free -m
```

## 4. Install & config containerd

- [install containerd](https://github.com/containerd/containerd/blob/main/docs/getting-started.md)
- [config containerd](https://github.com/containerd/containerd/blob/main/docs/getting-started.md#customizing-containerd)
- [containerd with kubernetes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd)

## 5. [Install kubeadm, kubelet, kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)

step 3. Add the Kubernetes `apt` repository: replace from `https://apt.kubernetes.io/` to `https://packages.cloud.google.com/apt/`

## 6. (optional)SSH nopassphrase connect

```shell
ssh-keygen -t ed25519
ssh-copy-id -i .ssh/id_ed25519.pub wck@192.168.0.101
or
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
```



# kubeadm install k8s

## 1. kubeadm init & join

- Pre-step

```shell
sysctl --system
modprobe br_netfilter
```

- Kubeadm init

```shell
sudo kubeadm init --apiserver-advertise-address=192.168.0.70 --pod-network-cidr=10.244.0.0/16
```

- Kubeadm init -  **Result**

```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.0.100:6443 --token g91vb0.m2a30efxycjg2598 \
	--discovery-token-ca-cert-hash sha256:acfd48c915ff858bb015885bef520bb76c859ab9358dea6b7756d3d992d1bc70 
```

- kubeadm join

```shell
kubeadm join 192.168.0.100:6443 --token 7urp37.0fr8vwtzxmvaglih \
	--discovery-token-ca-cert-hash sha256:6380e00b266f4638aed95e007dd4197b0ef58450e70b9b23dbda59cb2f13687a 
```

## 2. [Install Calico](https://projectcalico.docs.tigera.io/getting-started/kubernetes/self-managed-onprem/onpremises)

- `ufw allow 179`

- Result

  ![image-20221211195802898](/Users/wck/Library/Application Support/typora-user-images/image-20221211195802898.png)

## 3. [Kubernetes Dashboard](https://www.google.com/url?client=internal-element-cse&cx=013288817511911618469:elfqqbqldzg&q=https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/&sa=U&ved=2ahUKEwjDhLb7wPH7AhXbklYBHYwzA9UQFnoECAQQAg&usg=AOvVaw3_SHfKmduRpXBrdqWnl2Ef)

- NodePort

  <img src="/Users/wck/Library/Application Support/typora-user-images/image-20221212000656008.png" alt="image-20221212000656008" style="zoom:50%;" />

  ```
  kubectl -n kubernetes-dashboard create token kubernetes-dashboard
  ```

## 4. Ingress



