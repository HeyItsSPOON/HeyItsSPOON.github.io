---
title: kubernetes 개념 및 cluster 설치 방법 (2)
description: kubernetes cluster 설치
categories:
 - project
tags: kubernetes
---

> kubernetes 개념 및 cluster 설치 방법 (2) - kubernetes cluster 설치

[1. kubernetes 개념 및 cluster 설치 방법 (1) - kubernetes 개념 정리](https://heyitsspoon.github.io/project/2022/05/25/kubernetes-1/)

[2. kubernetes 개념 및 cluster 설치 방법 (2) - kubernetes 개념 정리](https://heyitsspoon.github.io/project/2022/06/05/kubernetes-2/)

# 목차

[17. 마치며](#마치며)

# Cluster 구조

| hostname | ip | 역할 |
| --- | --- | --- |
| k8s-master1 | 10.1.1.1 | 마스터 노드 |
| k8s-master2 | 10.1.1.2 | 마스터 노드 |
| k8s-worker1 | 10.1.1.3 | 워커 노드 |
| k8s-worker2 | 10.1.1.4 | 워커 노드 |
| k8s-worker3 | 10.1.1.5 | 워커 노드 |
| gitlab | 10.1.1.6 | pv 저장소 |

# hosts 등록

```bash
# vim /etc/hosts
10.1.1.1 k8s-master1
10.1.1.2 k8s-master2
10.1.1.3 k8s-worker1
10.1.1.4 k8s-worker2
10.1.1.5 k8s-worker3
10.1.1.6 gitlab
```

# 서버 시간 동기화

```bash
# dnf install chronyd
# vim /etc/chronyd.conf
```

<aside>
💡 Master node

</aside>

```bash
# /etc/chronyd.conf
server time.bora.net iburst
allow 10.1.1.0/24
```

<aside>
💡 Worker node

</aside>

```bash
# /etc/chronyd.conf
server k8s-master1 iburst
server k8s-master2 iburst
```

# 방화벽 설정

```bash
# systemctl stop firewalld
# systemctl disable firewalld
```

쿠버네티스는 기본적으로 iptables를 기본 방화벽 정책으로 사용하기 때문에, firewall은 내려주도록 한다.

```bash
# dnf install iptables-services
# vim /etc/sysconfig/iptables
```

<aside>
💡 Master node

</aside>

```bash
# /etc/sysconfig/iptables

#### KUBERNETES API 서버
-I INPUT -p tcp --dport 6334 -j ACCEPT # 기본 포트 변경

#### ETCD SERVER 클라이언트 API
-I INPUT -p tcp --dport 2379:2380 -j ACCEPT

#### KUBELET API
-I INPUT -p tcp --dport 10250 -j ACCEPT

#### KUBE-SCHEDULER
-I INPUT -p tcp --dport 10259 -j ACCEPT

#### KUBE-CONTROLLER-MANAGER
-I INPUT -p tcp --dport 10257 -j ACCEPT
```

<aside>
💡 Worker node

</aside>

```bash
#### KUBELET API
-I INPUT -p tcp --dport 10250 -j ACCEPT

#### NODEPORT SERVICE
-I INPUT -p tcp -m tcp --dport 30000:32767 -j ACCEPT
```

# 쿠버네티스 사용 모듈 로드

```bash
# cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

# cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
```

iptables가 브릿지 드라이브를 사용하여 통신하는 패킷을 filtering 할 수 있도록 하기 위해 br_netfilter 모듈을 적재한다.

이 모듈을 iptables가 사용 할 수 있도록 net.bridge.bridge-nf-call-iptables도 함께 적재한다.

# 환경 변수 설정

```bash
# vim /etc/profile
```

```bash
# /etc/profile
export KUBE_VERSION='1.23' # kubernetes version
```

# 컨테이너 런타임 다운로드

```bash
# curl -L -o \
/etc/yum.repos.d/devel:kubic:libcontainers:stable.repo \
[https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable/CentOS_8/devel:kubic:libcontainers:stable.repo](https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable/CentOS_8/devel:kubic:libcontainers:stable.repo)
# curl -L -o \
/etc/yum.repos.d/devel:kubic:libcontainers:stable:cri-o:${KUBE_VERSION}.repo [https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:${KUBE_VERSION}/CentOS_8/devel:kubic:libcontainers:stable:cri-o:${KUBE_VERSION}.repo](https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$%7BVERSION%7D/CentOS_8/devel:kubic:libcontainers:stable:cri-o:$%7BVERSION%7D.repo)

# dnf install cri-o cri-tools

# systemctl daemon-reload
# systemctl enable crio --now
```

# kubernetes 다운로드

```bash
# cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg [https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg](https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg)
exclude=kubelet kubeadm kubectl
EOF

# dnf install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
# systemctl enable --now kubelet
```

# NFS 구축

<aside>
💡 gitlab

</aside>

```bash
# dnf install -y nfs-utils
# systemctl start nfs-server
# systemctl enable nfs-server
# systemctl start rpcbind
# systemctl enable rpcbind
# mkdir /kube_store
# vim /etc/exports
```

```bash
# /etc/exports
/kube_store 10.1.1.0/24(rw,sync,no_root_squash)
```

<aside>
💡 All nodes

</aside>

```bash
# dnf install -y nfs-utils
# mkdir /kube_store
# showmount -e gitlab
# mount -t nfs gitlab:/kube_store /kube_store
# vim /etc/fstab
```

```bash
# /etc/fstab
gitlab:/kube_store /kube_store nfs sync 0 0
```

# kubernetes 설치

<aside>
💡 k8s-master1

</aside>

```bash
# vim /kube_store/kubeadm-config.yml
```

```bash
# /kube_store/kubeadm-config.yml
---
apiVersion: kubeadm.k8s.io/v1beta3
kind: InitConfiguration
localAPIEndpoint:
  bindPort: 6334
---
apiVersion: kubeadm.k8s.io/v1beta3
kind: ClusterConfiguration
kubernetesVersion: v1.23.0
controlPlaneEndpoint: "k8s-master1:6334"
apiServer:
  extraArgs:
    authorization-mode: "Node,RBAC"
networking:
  podSubnet: "192.168.0.0/16"
---
apiVersion: kubeadm.k8s.io/v1beta3
kind: JoinControlPlane
localAPIEndpoint:
  bindPort: 6334
---
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
cgroupDriver: systemd
```

```bash
# kubeadm init --config kubeadm-config.yml --upload-certs
```

# CNI 다운로드

<aside>
💡 k8s-master1

</aside>

```bash
# curl https://docs.projectcalico.org/manifests/calico.yaml -O --insecure
# kubectl apply -f clico.yaml
```

<aside>
💡 All nodes

</aside>

```bash
# vim /etc/sysconfig/iptables
```

```bash
# /etc/sysconfig/iptables

-I INPUT -p tcp --dport 179 -j ACCEPT
-I INPUT -p udp --dport 4789 -j ACCEPT
-I INPUT -p tcp --dport 5473 -j ACCEPT
-I INPUT -p tcp --dport 443 -j ACCEPT
```

```bash
# systemctl restart iptables
```

# kubernetes cluster 구축

<aside>
💡 k8s-master2

</aside>

```bash
# kubeadm join k8s-master1:6334 \
--token aaly0a.sp3dz8w4z8pe5dbb \
--discovery-token-ca-cert-hash sha256:a848eba0c188c12ab66aeb2359dee021f778ebd350f7c0c538949c7d1a57c320 \
--control-plane \
--certificate-key cc191962adc302a7127ad2ff830cacba816afb610018c70b4b7efcdff060764e \
--apiserver-bind-port 6334
```

<aside>
💡 Worker node

</aside>

```bash
# kubeadm join k8s-master1:6334 \
--token aaly0a.sp3dz8w4z8pe5dbb \
--discovery-token-ca-cert-hash sha256:a848eba0c188c12ab66aeb2359dee021f778ebd350f7c0c538949c7d1a57c320
```

# Storage Class

<aside>
💡 k8s-master1

</aside>

```bash
# cd /kube_store
# git clone https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner.git
# cd nfs-subdir-external-provisioner/deploy
# kubectl apply -f rbac.yaml
# vim deployment.yaml
```

```bash
# /kube_store/nfs-subdir-external-provisioner/deploy/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-client-provisioner
  labels:
    app: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: kube-system
spec:
  replicas: 1
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nfs-client-provisioner
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: k8s.gcr.io/sig-storage/nfs-subdir-external-provisioner:v4.0.2
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: k8s-sigs.io/nfs-subdir-external-provisioner
            - name: NFS_SERVER
              value: 10.1.1.6
            - name: NFS_PATH
              value: /kube_store
      volumes:
        - name: nfs-client-root
          nfs:
            server: 10.1.1.6
            path: /kube_store
```

```bash
# kubectl apply -f deployment.yaml
# kubectl apply -f class.yaml
```