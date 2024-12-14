RHEL 9.4

https://infotechys.com/install-a-kubernetes-cluster-on-rhel-9/

===================================================================== 
  16  swapoff -a
   17  sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF

   18  sudo modprobe overlay
   19  sudo modprobe br_netfilter
   20  sudo tee /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

   21  sysctl --system
   22  sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
   23  sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
   24  sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   25  sudo apt update
   26  sudo apt install -y containerd.io
   27  containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
   28  sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
   29  sudo systemctl restart containerd
   30  sudo systemctl enable containerd
   37  sudo apt-get install -y apt-transport-https ca-certificates curl gpg
   38  curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
   39  echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
   40  sudo apt-get update
   41  sudo apt-get install -y kubelet kubeadm kubectl
   42  systemctl enable --now kubelet
   43  kubeadm init
   44  mkdir -p $HOME/.kube
   45  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   46  sudo chown $(id -u):$(id -g) $HOME/.kube/config
   52  kubectl apply -f https://github.com/projectcalico/calico/blob/v3.28.0/manifests/calico.yaml
   53  kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
   54  kubectl get pods -n kube-system
   55  kubectl get nodes -o wide   
   
==========================================================================================

sudo swapoff -a
sudo sed -i '/swap/d' /etc/fstab
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing/SELINUX=permissive/' /etc/selinux/config
echo 1 > /proc/sys/net/bridge/bridge-nf-call-iptables
echo 1 > /proc/sys/net/ipv4/ip_forward
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y containerd.io
sudo sh -c "containerd config default > /etc/containerd/config.toml" ; cat /etc/containerd/config.toml
sudo vim /etc/containerd/config.toml
	SystemdCgroup = true
sudo systemctl enable --now containerd.service
reboot

vim /etc/yum.repos.d/kubernetes.repo

[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.31/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.31/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni

sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
systemctl enable --now kubelet.service

kubeadm init --pod-network-cidr=10.244.0.0/16

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0-beta.0/deploy/static/provider/cloud/deploy.yaml

========================================================================================

PROMETHEUS SETUP

https://www.bigbinary.com/blog/prometheus-and-grafana-integration     (follow this link)
Create new namespace and mention it in every command

vim ./custom_kube_prometheus_stack.yml

alertmanager:
  alertmanagerSpec:
    # Selects Alertmanager configuration based on these labels. Ensure that the Alertmanager configuration has matching labels.
    # ? Solves error: Misconfigured Alertmanager selectors can lead to missing alert configurations.
    # ? Solves error: Alertmanager wasn't able to findout the applied CRD (kind: Alertmanagerconfig)
    alertmanagerConfigSelector:
      matchLabels:
        release: monitoring

    # Sets the number of Alertmanager replicas to 3 for high availability.
    # ? Solves error: Single replica can cause alerting issues during pod failures.
    # ? Solves error: Alertmanager Cluster Status is Disabled (GitHub issue)
    replicas: 2

    # Sets the strategy for matching Alertmanager configurations. 'None' means no specific matching strategy.
    # ? Solves error: Incorrect matcher strategy can lead to unhandled alert configurations.
    # ? Solves error: Get rid of namespace matchers when creating AlertManagerConfig (GitHub issue)
    alertmanagerConfigMatcherStrategy:
      type: None
	  
helm install my-kube-prometheus-stack prometheus-community/kube-prometheus-stack -n observability -f ./custom_kube_prometheus_stack.yml

Prometheus : pod -- api server -- kube-state-metrix -- Prometheus
Grafana : Support other Data sources, Better Dashboard, Authentication and Autherization
