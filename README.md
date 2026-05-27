# 🧩 Kubernetes + Istio Ambient + Argo CD GitOps Lab (VM 기반)


## ✒ 목적

기존에는 단순히 쿠버네티스 클러스터를 띄우는 것에 집중했다면, 이번 실습에서는 </br></br>
**Cilium + Istio Ambient(ztunnel, waypoint) + Argo CD GitOps**를 하나의 흐름으로 묶어 운영 관점에서 검증하는 데 초점을 맞췄습니다. </br></br>

특히 Ambient Mesh에서 **L4(ztunnel)와 L7(waypoint) 처리 경로가 실제로 어떻게 분리되는지**, </br>
그리고 GitOps 환경에서 Helm values 기반으로 **dev/staging/prod 다중 환경 배포와 self-heal**이 어떻게 동작하는지 확인하고자 했습니다. </br></br>

단순 설치가 아니라, 실제로 발생한 **Cilium ↔ Istio CNI 충돌**, Argo CD 동기화 오류 등 장애 상황을 직접 겪고 해결하면서 </br>
트러블슈팅 관점의 운영 감각을 함께 익히는 것을 목표로 했습니다.

</br>

---

## ⚙ 초기설정

### 🏗️ Kubernetes Cluster (kubeadm)

**Control Plane** / IP: `10.0.2.41` </br>
- kubeadm control-plane
- containerd runtime
- Argo CD / Istio control components 실행

**Worker1** / IP: `10.0.2.42` </br>
- Cilium agent
- ztunnel / waypoint / 앱 워크로드 실행

**Worker2** / IP: `10.0.2.43` </br>
- Cilium agent
- ztunnel / waypoint / 앱 워크로드 실행

</br>

### 🧱 네트워크/런타임 기본값

- OS: Ubuntu 26.04
- Kubernetes: `v1.30.14`
- Container Runtime: `containerd://2.2.2`
- Pod CIDR: `10.244.0.0/16`
- Node CIDR 대역: `10.0.2.0/24`

</br>

### 🌐 CNI / Service Mesh / GitOps

- CNI: **Cilium 1.19.4**
- Service Mesh: **Istio 1.30.0 (Ambient Profile)**
- GitOps: **Argo CD v3.4.2**

</br>

---

## 🏗️ Architecture

<img width="1670" height="941" alt="Image" src="https://github.com/user-attachments/assets/b65a549f-673f-4821-9739-d701a41fe522" />

- North-South 트래픽: External → Istio Ingress Gateway
- East-West L4: ztunnel (HBONE)
- East-West L7: waypoint (Envoy)
- GitOps: GitHub(main) → Argo CD → Kubernetes

</br>

---

