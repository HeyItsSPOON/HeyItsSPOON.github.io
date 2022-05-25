---
title: kubernetes 개념 및 cluster 설치 방법 (1)
description: kubernetes 개념 정리
categories:
 - project
tags: kubernetes
---

> kubernetes 개념 및 cluster 설치 방법 (1) - kubernetes 개념 정리

[1. kubernetes 개념 및 cluster 설치 방법 (1) - kubernetes 개념 정리](https://heyitsspoon.github.io/project/2022/05/25/kubernetes-1/)

# 목차
[1. 목차](#목차)

[2. 개요](#개요)

[3. 컨테이너란?](#컨테이너란?)

[4. 마치며](#마치며)

# 개요
오랜만에 글을 작성합니다. 그 동안 놀았던 것은 아니고 여러 일이 겹치는 바람에 포스팅을 하지 못했습니다.
최근 회사는 kvm, hyper-v, openstack 등 가상화 관련한 프로젝트를 시작했습니다. 이번 포스팅에서는 가상화 중에서도 컨테이너 기반 가상화와 관련된 쿠버네티스에 대해 다뤄볼까 합니다.
사실 쿠버네티스보다 다른 가상화 스터디를 먼저 시작하였지만, AWX 공부를 시작하면서 쿠버네티스를 가장 먼저 구축하게 되었기 때문에 쿠버네티스 포스팅을 가장 먼저 시작하게 되었습니다.
이번 포스팅에서는 쿠버네티스와 관련 된 개념을 먼저 살펴보고, 다음 포스팅에서 본격적으로 쿠버네티스 클러스터를 구축 해 보겠습니다.

# 컨테이너란?
쿠버네티스를 다루면서 
소프트웨어 서비스를 실행하는 데 필요한 특정 버전의 프로그래밍 언어 런타임 및 라이브러리와 같은 종속 항목과 애플리케이션 코드를 함께 포함하는 경량 패키지.

서비스에 필요한 관련 패키지, 데몬을 한데 묶어서 작은 서버를 구축 후 배포하는 개념.

호스트 OS의 리소스를 논리적으로 분리하고, 여러 개의 컨테이너가 공유해서 사용한다. VM과 달리 운영체제 커널을 공유하므로 훨씬 빠르고 메모리도 적게 사용한다.

# kubernetes란?

![kubernetes.png](https://heyitsspoon.github.io/assets/images/kubernetes/kubernetes.png)

: 컨테이너화된 애플리케이션을 자동으로 배포, 스케일링 및 관리해주는 오픈소스 시스템.  컨테이너 오케스트레이션 기능을 지원한다. 노드 클러스터링, 로드 밸런싱, 배포 및 복제 자동화, 장애 복구 기능, 오토스케일링, 스케줄링, 로깅 및 모니터링 등을 지원한다.

# 도커와 쿠버네티스 차이?

- 도커 : 도커는 컨테이너 기반의 오픈소스 플랫폼. 즉 컨테이너 실행에 필요한 파일과 설정값인 이미지를 컨테이너에 띄우고 실행 할 수 있는 기술. 한개의 서버에 컨테이너를 관리한다면 Docker가 적합.
- 쿠버네티스 : 쿠버네티스는 컨테이너를 쉽고 빠르게 배포/확장하고 관리를 자동화해주는 오픈소스 플랫폼. 컨테이너 시장이 커지고 점점 더 많은 컨테이너를 관리, 배포해야 상황에서 사용하기 좋음.

# cgroup이란?[^1]

![cgroup_process.png](https://heyitsspoon.github.io/assets/images/kubernetes/cgroup_process.png)

: 단일, 또는 태스크 단위의 프로세스 그룹에 대한 자원 할당을 제어하는 커널 모듈. 컨테이너에서는 컨테이너가 생성되면 컨테이너의 프로세스들을 담당하는 컨테이너 cgroup이 생성된다. 컨테이너 내부 프로세스가 fork system call을 통해서 자식 프로세스들을 생성해도 생성된 자식 프로세스 역시 컨테이너 cgroup에 소속되기 때문에 컨테이너의 리소스를 제어하기 위해서는 컨테이너 cgroup을 제어하면 된다.

cgroup을 제어하는 방법에는 cgroupfs와 systemd에서 제공하는 API를 사용하는 방법이 있다.

![cgroup.png](https://heyitsspoon.github.io/assets/images/kubernetes/cgroup.png)

- cgroupfs : cgroup 제어를 위해 탄생한 특수 file system. cgroup 정보는 리눅스 커널 내부에서 관리하고 있으며 리눅스 커널은 cgroup 제어를 위해 별도의 system call을 제공하지 않고 cgroupfs를 제공하고 있다. cgroupfs를 사용하여 cgroup을 관리하는 방법은 cgroupfs에 디렉토리를 생성하면 된다. 경로는 /sys/fs/cgroup/memory.
- systemd : 리눅스의 init process이며 기존 기능은 데몬 프로세스 제어이지만 cgroup을 제어하는 역할도 수행한다. systemd의 cgroup 제어 기능은 자신이 제어하는 데몬 프로세스의 리소스 사용량을 제어하기 위해 사용된다. systemd-run 명령어를 통해 unit을 생성하고 실행하면 cgroup이 생성된다.

결국 cgroup을 제어하기 위해 두 가지 방식이 사용되는데, 두 가지 방식의 차이점은 요청하는 주체가 다른 것일 뿐 동일한 동작을 한다. cgroupfs 드라이버 방식을 사용 시 쿠버네티스가 직접 cgroup 디렉토리에 관여를 하고, systemd 드라이버 방식을 사용 시 systemd API를 통해 cgroup 디렉토리에 관여를 하기 때문에 두 방식의 cgroup 생성 경로가 달라지게 된다.

systemd는 사용자 레벨의 어플리케이션이고 cgroupfs는 직접 cgroup을 관리하는 방식이기 때문에 안정성 차이는 있을 수 있으나 성능상의 차이점은 없다. 다만 kubernetes 공식 문서에는 kubelet 및 도커와 다른 프로세스가 서로 다른 cgroup 제어 정책을 사용하게 되면 리소스가 부족할 때 불안정해지는 사례가 있었다고 보고되었다.

# 컨테이너 런타임이란?

: 컨테이너 실행을 담당하는 소프트웨어. 대표적으로 dockershim, containerd, CRI-O 등이 있다.

- docker-shim : 도커 호환성을 위해 쿠버네티스에서 자체적으로 개발 한 도커 컨테이너 런타임 툴
경로 : /var/run/dockershim.sock
- containerd : 도커 사에서 개발한 컨테이너 런타임 프로젝트. 
경로 : /run/containerd/containerd.sock
- CRI-O : 레드햇이 개발한 쿠버네티스를 위한 표준 컨테이너 런타임. 기본적인 cgroup 관리 모듈로 systemd를 사용한다. 
경로 : /var/run/crio/crio.sock

# CRI란?

: CRI가 생기기 전에는 대표적인 컨테이너 런타임 툴인 도커와 rkt가 kubelet에 직접 통합되어있었으나, 이 외에도 새로운 컨테이너 런타임 툴이 생겨나면서 쿠버네티스에서 다양한 런타임 툴을 지원하기 위해 런타임간의 인터페이스를 통일화 할 수 있게 해주는 CRI를 개발하였다.

따라서 모든 컨테이너 런타임 툴은 CRI 스펙에 맞춰 컴포넌트를 구현하면 kubelet과 호환 할 수 있다.

# 쿠버네티스에서 도커 런타임 지원을 중단 한 이유?[^2]

: 컨테이너 런타임과 호환성이 없기 때문에. Docker는 기본적으로 쿠버네티스에 통합되도록 설계되어있지 않기 때문에 호환성에 많은 문제가 있다.

도커가 먼저 개발되었기 때문에 도커는 쿠버네티스의 CRI기준에 맞지 않아 쿠버네티스에서 도커를 지원하기 위한 도커 런타임인 docker-shim을 개발하게 됨.

그러나 쿠버네티스에서 docker-shim 유지보수에 어려움을 느꼈고, docker에서는 CRI 기준에 맞춘 런타임 툴을 따로 개발하지 않아 지원을 중단하게 되었다.

이 외에도 docker의 모든 명령은 root 권한을 이용해야하기 때문에 보안상의 문제도 있고, 데몬 형태로 실행되는 도커는 이미지 빌드, 관리, 공유, 실행 등 도커 자체에도 많은 기능이 있고 데몬 하위에 모든 컨테이너를 자식 프로세스로 소유하고 있어 쿠버네티스 상에서 필요하지 않은 기능으로 인해 쿠버네티스가 무거워지고, 도커 데몬이 중지되면 쿠버네티스 상에서도 영향이 미치기 때문에 도커의 런타임 지원을 중단하였다.

# 어떤 컨테이너 런타임을 선택해야 할까?[^3]

관련 논문 각주 참조

다만 CRI-O는 쿠버네티스에서 컨테이너 실행에 대한 부분만 관여하기 때문에 추가 기능을 사용하기 위해선 컴포넌트 설치가 필요하다.

- crictl : 쿠버네티스 컨테이너 런타임 및 애플리케이션 검사, 디버깅
- runc : 컨테이너 이미지 실행
- podman : 컨테이너 엔진의 외부에서 Pod와 컨테이너 이미지 관리 ( run, stop, start, ps, attach, exec ) 등
- buildah : 컨테이너 이미지 빌드 및 푸시
- skopeo : 이미지 복사, 검사, 제거

# 쿠버네티스 용어

- kubeadm :  쿠버네티스에서 제공하는 기본적인 도구, 쿠버네티스 클러스트 구축 관련 명령어.
- kubelet : 클러스터 내의 모든 노드에서 실행되는 에이전트. pod 내의 컨테이너들이 실행되는 것을 직접적으로 관리 및 상태 체크. 또한 master node와 worker node 간 통신을 책임진다.
- kubectl : 쿠버네티스 클러스터를 제어하기 위한 커맨드 라인 도구.

# 쿠버네티스 아키텍처

![쿠버네티스 클러스터 아키텍처](https://heyitsspoon.github.io/assets/images/kubernetes/kubernetes-architecture.png)

쿠버네티스 클러스터 아키텍처

- ETCD : 노드 정보 등 쿠버네티스 클러스터 데이터를 저장하는 고가용성 키-값 저장소, etcd 데이터 백업 계획은 필수이다.
- kube-apiserver : 쿠버네티스 API, 외/내부에서 관리자의 원격 명령을 받을 수 있는 컴포넌트 쿠버네티스의 frontend. 작업 상태를 정의하고 조회 할 수 있는 RESTful 웹 서비스를 제공한다. 쿠버네티스의 각 구성 요소는 API 서버를 거쳐서 서로 통신하게 된다.
- kube-controller-manager : 쿠버네티스는 현재 시스템을 사용자가 정의한 상태로 맞춰준다. 오브젝트의 현재 상태를 지속적으로 체크하고 상태를 제어한다. 컨트롤러 프로세스를 실행하고 클러스터의 실제 상태를 원하는 사양으로 조정한다.

       - Node Controller : 노드가 다운되었을 때 알림과 대응에 관한 역할을 한다.

       - Replication Controller : 지정 된 수의 pod들을 유지시켜 주는 역할을 한다.

       - Endpoints Controller : 서비스와 pod를 연결시켜 endpoint 오브젝트를 만든다.

       - Service Account & Token Controllers : 새로운 네임스페이스에 대한 기본 계정과 API 접근 토큰을 생성한다.

- kube-scheduler : 생성 된 pod를 노드에 할당 해 주는 컴포넌트. 리소스, 하드웨어, 소프트웨어, 정책, 워크로드 등을 참고하여 가장 최적화 된 노드에 pod를 배치한다.
- kube-proxy : 네트워크 연결을 관리하고 노드간에 네트워크 규칙을 유지한다.

# 네트워크 플러그인

![singenode-network.png](https://heyitsspoon.github.io/assets/images/kubernetes/singenode-network.png)

싱글 노드에서 Pod to Pod 통신 시 쿠버네티스에서 기본으로 제공하는 kubenet을 통해 간단한 네트워크 플러그인을 제공해준다. 하지만 이 네트워크 플러그인은 `크로스 노드 네트워킹`이나 `네트워크 정책 설정`과 같은 고급 기능은 구현되어있지 않다. 따라서 CNI(Container Network Interface)의 스펙을 준수하는 네트워크 플러그인을 따로 사용해야한다. 

![multinode-network.png](https://heyitsspoon.github.io/assets/images/kubernetes/multinode-network.png)

기존 네트워크 구조로 여러 노드를 구성하게 되면 각 노드에 중복되는 ip가 할당되기 때문에 요청이 왔을 때 어느 노드의 파드로 패킷이 전달되어야 하는지 알 수 없다.

따라서 쿠버네티스 네트워크에 오버레이 네트워크를 구성해주고 파드와 호스트 인터페이스를 연결해주는 부분을 별도의 모듈로 분리한 CNI가 필요하다.

CNI는 커널 라우팅, 동적 라우팅, Pod 인터페이스 생성, IP / Subnet / Routing Table 설정, Proxy ARP 기능 등을 수행한다.

# Application 배포 방법

- Replicaset : pod의 복제본을 관리하는 컨트롤러.

![deployment.png](https://heyitsspoon.github.io/assets/images/kubernetes/deployment.png)

- Deployment : stateless 앱을 배포할 때 사용되는 가장 기본이 되는 컨트롤러. deployment는 replicaset을 관리하게 되는데 배포를 위한 기능을 제공한다. 롤링업데이트, 배포 중단, 롤백 등이 가능하다. deployment controller는 지속적으로 배포 된 인스턴스들을 모니터링하고, 노드가 다운되거나 삭제되면 해당 컨트롤러가 클러스터 내부의 다른 노드 인스턴스로 교체해준다.

# 쿠버네티스 서비스 종류

![ClusterIP.png](https://heyitsspoon.github.io/assets/images/kubernetes/ClusterIP.png)

- ClusterIP : 클러스터 내부에서 해당 ClusterIP를 통해 Pod to Pod 통신을 가능하게 한다. ClusterIP는 서비스 (Deployment / Replicaset ) 단위로 생성되므로, ClusterIP 주소로 서비스를 호출하면 backend 단의 Pod 중 하나로 프록시된다.

![NodePort.png](https://heyitsspoon.github.io/assets/images/kubernetes/NodePort.png)

- NodePort : 클러스터에 속한 Node의 IP:NodePort를 Pod의 Service Port와 매핑시킨다.

![Load Balancer.png](https://heyitsspoon.github.io/assets/images/kubernetes/Load_Balancer.png)

- LoadBalancer : 외부의 LB를 생성하여, 외부 LB에서 NodePort로 들어오는 트래픽을 프록시한다.

![Ingress.png](https://heyitsspoon.github.io/assets/images/kubernetes/Ingress.png)

- 번외) Ingress : 서비스의 종류는 아니지만, 외부에서 클러스터 내부로 들어오는 네트워크 요청을 처리 할 방법을 정의한다.

<aside>
💡 Ingress와 서비스의 차이?
가장 큰 차이점은 서비스는 L4단위, Ingress는 L7단위의 제어가 가능하다는 점이다.
일반적인 경우에서는 Load Balancer로도 충분히 서비스 운영이 가능하지만, 서비스간의 라우팅이 필요한 구조에서는 Ingress를 사용한다.

</aside>

# 쿠버네티스 스토리지

![pv_pvc.png](https://heyitsspoon.github.io/assets/images/kubernetes/pv_pvc.png)

- pv : 인프라 관리자가 사전에 정의 해 놓은 볼륨
- pvc : 사용자 ( 개발자 ) 가 pod를 생성하기 위해 요청하는 볼륨
- Storage Class : 매번 pvc를 요청 할 때 마다 pv 를 생성할 수가 없기 때문에, 맞는 pv가 없을 시 자동으로 pv를 생성할 수 있도록 미리 pv를 생성 할 공간을 선언.

***

[^1]: 참고 : [https://tech.kakao.com/2020/06/29/cgroup-driver/](https://tech.kakao.com/2020/06/29/cgroup-driver/)

[^2]: 참고 : [https://ikcoo.tistory.com/189](https://ikcoo.tistory.com/189)

[^3]: 관련 논문 참조 : [https://www.scitepress.org/Papers/2020/93404/93404.pdf](https://www.scitepress.org/Papers/2020/93404/93404.pdf)