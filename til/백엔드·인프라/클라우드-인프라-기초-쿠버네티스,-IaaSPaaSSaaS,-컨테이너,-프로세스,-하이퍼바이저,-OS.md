---
title: "클라우드 인프라 기초: 쿠버네티스, IaaS/PaaS/SaaS, 컨테이너, 프로세스, 하이퍼바이저, OS"
tags: ["CS"]
date: 2026-09-02
notion_id: 3cf922cf-26a8-81c1-8fb6-d1bde3d96413
notion_last_edited: 2026-09-02T09:19:00.000Z
synced_at: 2026-09-02
---
> 📅 **학습일**: 2026-09-02

클라우드 모니터링 제품을 개발하면서 "쿠버네티스를 구축한다"는 말이 왜 "사용한다"가 아닌지 궁금해져서 시작한 질문이 계속 아래로 내려갔다. 쿠버네티스 → IaaS/PaaS/SaaS → 컨테이너 → 프로세스·하이퍼바이저 → OS 순으로 내려가며 정리한 것.


## 1. 쿠버네티스(K8s)


한 줄 정의: 컨테이너를 여러 대의 서버에 걸쳐 자동으로 배치하고, 죽으면 살리고, 늘리고 줄이는 시스템.


핵심 기능:

- 스케줄링: 컨테이너(Pod)를 어느 서버(Node)에 띄울지 CPU·메모리 여유를 보고 자동 결정
- 자가 복구(Self-healing): 컨테이너나 서버가 죽으면 다른 서버에 재기동
- 스케일링: 트래픽에 따라 복제본 수를 수동 또는 자동(HPA)으로 조절
- 서비스 디스커버리 / 로드밸런싱: 컨테이너 IP가 바뀌어도 고정 이름(Service)으로 접근
- 무중단 배포 / 롤백: Rolling update로 하나씩 교체, 문제 시 이전 버전으로 복귀
- 설정·비밀 관리: ConfigMap, Secret
- 스토리지 연결: PV/PVC
- 선언적 관리: YAML에 목표 상태를 적으면 현재 상태를 그에 맞게 계속 조정

왜 쓰나: 도커만으로는 컨테이너 수십~수백 개, 서버 여러 대 규모에서 배치·재시작·배포·서버 증감을 사람이 직접 해야 한다. 개발자는 "무엇을 띄울지"만 선언하고 "어떻게 유지할지"는 시스템이 맡는다. 소규모라면 오버스펙이고 도커 컴포즈로 충분한 경우가 많다.


왜 "사용"이 아니라 "구축"인가: 라이브러리가 아니라 플랫폼(인프라)이기 때문. 컨트롤 플레인 배치, 워커 노드 수, 네트워크 플러그인(CNI), 스토리지, Ingress, 모니터링·로깅·인증 체계를 직접 결정하고 조립해야 한다. 여러 부품을 조합해 운영 환경을 세우는 일이라 건물처럼 "구축한다"고 한다. 인프라 담당자는 구축하고 개발자는 그 위에 배포하며 사용한다. EKS, GKE 같은 관리형은 구축 부담이 줄어 "사용"에 가까워진다.


## 2. IaaS / PaaS / SaaS


핵심은 "어디까지 내가 관리하고 어디부터 제공자가 관리하는가". 서비스에 필요한 계층은 위에서부터 애플리케이션 → 데이터 → 런타임 → 미들웨어 → OS → 가상화 → 서버 → 스토리지 → 네트워크.


| 계층          | 온프레미스 | IaaS | PaaS | SaaS |
| ----------- | ----- | ---- | ---- | ---- |
| 서버·네트워크·가상화 | 나     | 제공자  | 제공자  | 제공자  |
| OS          | 나     | 나    | 제공자  | 제공자  |
| 런타임·미들웨어    | 나     | 나    | 제공자  | 제공자  |
| 애플리케이션      | 나     | 나    | 나    | 제공자  |
| 데이터         | 나     | 나    | 나    | 제공자  |


같은 앱(React + Node + PostgreSQL)을 배포한다면:

- IaaS(AWS EC2): 가상 서버를 만들고 SSH 로 들어가 Node, nginx, PostgreSQL 을 직접 설치. OS 패치, 재시작, HTTPS 인증서까지 전부 내 일
- PaaS(Vercel, Render, RDS): GitHub 레포를 연결하고 환경변수만 넣으면 끝. 서버가 어디 있는지 모름
- SaaS: 앱을 만들 필요 자체가 없음. 완성된 서비스를 결제해서 사용

헷갈리기 쉬운 지점:

- AWS 는 "회사"이고 그 안에 IaaS(EC2, EBS, VPC), PaaS(Elastic Beanstalk, RDS, Lambda, EKS), SaaS(Chime, WorkMail) 가 다 있다. 어느 상품을 쓰느냐로 갈린다
- VM 을 직접 만들고 설치하는 것이 IaaS. PaaS 는 VM 이 있는지조차 신경 안 쓴다
- SSH 로 서버에 들어갈 수 있느냐가 IaaS 와 PaaS 를 가르는 가장 쉬운 기준
- 온프레미스는 장소가 아니라 "누가 관리하느냐"의 문제. 설치형 소프트웨어를 고객이 자기 EC2 에 올려도 제품 입장에서는 여전히 온프레미스 방식
- 쿠버네티스는 IaaS 위에 올려서 PaaS 처럼 쓰는 도구. 직접 구축하면 "내부 PaaS 를 만드는 일"

파생: FaaS(Lambda, 함수 단위 실행), DBaaS(RDS), CaaS(EKS, GKE), XaaS(통칭)


## 3. 컨테이너는 도커가 아니다


컨테이너는 리눅스 커널에 원래 있던 격리 기능 두 가지를 묶어 부르는 이름이다. 도커는 그걸 쓰기 쉽게 만든 도구 중 하나 (브라우저와 크롬의 관계).

- namespace: 프로세스에게 자기만의 프로세스 목록·네트워크·파일시스템을 보여줘서 다른 프로세스가 안 보이게 함
- cgroup: 프로세스가 쓸 수 있는 CPU·메모리·디스크 I/O 를 제한
- 결국 컨테이너는 리눅스 프로세스다. 호스트에서 ps 를 치면 그대로 보인다
- FreeBSD jail, Solaris Zones, LXC 가 2000년대 초반부터 있었고 도커는 2013년. 도커가 한 일은 docker run 한 줄로 쓰게 하고 이미지라는 패키징 형식을 만든 것

|       | VM           | 컨테이너      |
| ----- | ------------ | --------- |
| 격리 단위 | OS 전체(커널 포함) | 프로세스      |
| 커널    | 각자 자기 커널     | 호스트 커널 공유 |
| 기동 시간 | 수십 초~분       | 밀리초~초     |
| 크기    | 수 GB         | 수십~수백 MB  |
| 격리 강도 | 강함           | 상대적으로 약함  |


도커 없는 컨테이너 생태계: OCI(표준) → 도커 CLI / Podman(사람이 쓰는 도구) → containerd / CRI-O(생명주기 관리) → runc(커널에 격리 설정) → 리눅스 커널. 쿠버네티스는 v1.24(2022년)부터 도커를 직접 지원하지 않고 containerd 를 쓴다. 도커 이미지는 OCI 표준이라 그대로 동작한다.


## 4. 프로세스와 하이퍼바이저


프로세스: 실행 중인 프로그램 하나. OS 가 관리하는 실행 단위. PID, 자기만의 메모리 공간, 열어둔 파일·네트워크 연결을 가진다. 서버에서는 nginx, node, postgres, sshd 가 각각 프로세스로 돌면서 하나의 서비스를 이룬다.


하이퍼바이저: 한 대의 물리 서버 위에 VM 여러 대를 만들고 관리하는 소프트웨어. CPU 32코어를 "이 VM 은 4코어, 저 VM 은 8코어" 식으로 쪼개고 각 VM 은 자기 OS 를 부팅한다.

- Type 1(베어메탈): 하드웨어 위에 직접. VMware ESXi, KVM, Hyper-V, Xen
- Type 2(호스티드): 일반 OS 위에 앱처럼. VirtualBox, Parallels
- AWS EC2 는 데이터센터 물리 서버에 하이퍼바이저(Nitro)를 깔고 VM 을 빌려주는 것. IaaS 의 "가상화" 계층이 하이퍼바이저

```javascript
[VM 방식]                      [컨테이너 방식]

 앱 프로세스   앱 프로세스        앱  앱  앱 (namespace/cgroup 격리)
 게스트 OS     게스트 OS
 ─────────────────────        ───────────────────────────
     하이퍼바이저                    호스트 OS (리눅스 커널)
 ─────────────────────        ───────────────────────────
     물리 서버                        물리 서버
```


하이퍼바이저는 하드웨어를 쪼개서 OS 를 여러 개 올리고, 컨테이너는 OS 하나 안에서 프로세스를 격리한다. 실제 클라우드에서는 둘을 겹쳐 쓴다: 하이퍼바이저로 VM(EC2) → 그 안에서 컨테이너 수십 개(도커) → VM 여러 대를 묶어 배치 자동화(쿠버네티스).


## 5. OS(Operating System, 운영체제)


하드웨어와 프로그램 사이에서 중개하고, 여러 프로그램이 하드웨어를 나눠 쓰게 관리하는 소프트웨어.

- 프로세스 관리: 프로그램을 프로세스로 만들고 CPU 시간을 번갈아 배정(스케줄링). 쿠버네티스의 스케줄링은 이 개념을 서버 여러 대 규모로 확장한 것
- 메모리 관리: 프로세스별 메모리 분배, 남의 메모리 침범 차단
- 파일시스템: 디스크의 0과 1을 폴더와 파일로 보여줌
- 장치·네트워크: 드라이버로 장치를 다루고 TCP/IP 처리

커널은 OS 의 핵심 부분으로 위 일을 실제로 한다. OS = 커널 + 기본 도구(셸, 라이브러리, 시스템 서비스). Ubuntu, CentOS, Debian 은 커널이 같고 그 위의 도구만 다른 "배포판". 컨테이너가 "호스트 커널을 공유한다"는 말은 컨테이너 안에 Ubuntu 이미지를 써도 커널은 호스트의 것을 쓴다는 뜻. 이미지에는 커널이 없어서 가볍다.


IaaS 와 PaaS 를 가르는 선이 "OS 를 누가 관리하느냐"였는데, OS 관리란 보안 패치, 커널 업데이트 후 재부팅, 디스크·로그 정리, 계정·권한, 방화벽 설정 같은 일이다.


## 6. 터미널에서 OS 통계 보기


OS 는 자기가 관리하는 모든 것의 통계를 갖고 있고 리눅스는 그걸 /proc 과 /sys 가상 파일로 노출한다. top, ps, free 같은 명령은 이 파일을 읽어 예쁘게 보여주는 것이고, 모니터링 에이전트도 똑같이 이 파일을 읽는다. PaaS 에서 에이전트를 설치할 수 없는 이유가 /proc 을 읽을 권한이 없기 때문이다.


맥에서 top -l 1 을 실행한 실제 출력 상단:


```javascript
Processes: 722 total, 2 running, 720 sleeping, 4451 threads
Load Avg: 1.15, 1.29, 1.30
CPU usage: 3.33% user, 9.1% sys, 87.64% idle
PhysMem: 23G used (2411M wired, 5983M compressor), 66M unused
```

- Processes: 722개 중 2개만 실제로 CPU 를 쓰는 중
- Load Avg: 최근 1·5·15분 동안 CPU 를 기다리는 작업의 평균 개수. 코어 수보다 작으면 여유
- CPU usage: user 는 앱, sys 는 커널, idle 은 노는 시간

| 보고 싶은 것  | 맥                                  | 리눅스                 |
| -------- | ---------------------------------- | ------------------- |
| 전체 요약    | top                                | top, htop           |
| 부하·가동 시간 | uptime                             | uptime              |
| 프로세스 목록  | ps aux                             | ps aux              |
| 메모리      | vm_stat                            | free -h             |
| 디스크 용량   | df -h                              | df -h               |
| 디스크 I/O  | iostat                             | iostat, vmstat      |
| 네트워크 연결  | lsof -i                            | ss -tulnp           |
| CPU 정보   | sysctl -n machdep.cpu.brand_string | lscpu               |
| OS 버전    | sw_vers                            | cat /etc/os-release |
| 원본 통계 파일 | 없음                                 | /proc, /sys         |


```bash
# CPU 많이 쓰는 프로세스 상위 10개
ps aux | sort -rk 3 | head -10

# 어떤 프로세스가 3000 포트를 쓰는지
lsof -i :3000

# 리눅스에서만: /proc 직접 읽기
cat /proc/meminfo
cat /proc/loadavg
cat /proc/1234/status
```


맥은 커널이 다르기 때문에(XNU) /proc 이 없고 sysctl 과 Mach API 로 같은 정보를 꺼낸다.


## 7. 이미지는 애플리케이션이 아니다


이미지는 "실행 환경을 통째로 얼려둔 스냅샷 파일". 애플리케이션은 그 안의 내용물 중 하나다. 도시락에 빗대면 이미지가 도시락, 앱은 반찬 하나. 밥(OS 도구), 젖가락(런타임), 뿌꺽에 적힌 먹는 법(시작 명령)이 다 들어 있다. 이미지는 읽기 전용 원본이고 실행하면 복사본이 컨테이너 또는 VM 이 된다. 프로그래밍으로는 이미지 = 클래스, 컨테이너 = 인스턴스.


컨테이너 이미지를 뚯어보면:


```javascript
레이어 5: CMD ["node", "server.js"]        ← 시작 명령 (메타데이터)
레이어 4: /app/server.js, /app/package.json ← 내 애플리케이션 코드
레이어 3: /app/node_modules/...            ← npm install 결과
레이어 2: /usr/bin/node                     ← Node 런타임
레이어 1: /bin, /lib, /etc, /usr ...        ← Ubuntu 기본 파일 (커널 제외)
```

- 파일시스템 덩어리다. 풀어보면 리눅스 폴더 구조가 나온다. 커널이 없기 때문에 호스트 커널을 공유한다
- 레이어로 쌓인다. Dockerfile 한 줄이 레이어 하나. 같은 베이스 레이어는 디스크에 한 번만 저장된다
- 불변이다. 컨테이너 안에서 파일을 바꿔도 이미지는 안 바뀜다. 컨테이너를 지우면 변경도 사라진다
- Dockerfile 은 이미지를 만드는 레시피. docker build → 이미지, docker run → 컨테이너

```docker
FROM node:20               # Node 가 깔린 Ubuntu 를 바탕으로
COPY . /app                # 내 코드 복사
RUN npm install            # 의존성 설치
CMD ["node", "server.js"]  # 시작 명령
```


VM 이미지는 같은 개념인데 범위가 더 크다. 커널을 포함한 OS 전체와 부트로더까지 들어 있는 디스크 복제본(.vmdk, .qcow2, AMI)이고 수 GB 이상이다. EC2 를 만들 때 "Ubuntu 22.04" 를 고르는 것이 AMI 를 고르는 것.


|      | 이미지               | 실행된 것             |
| ---- | ----------------- | ----------------- |
| 컨테이너 | 파일시스템 레이어 + 메타데이터 | 컨테이너 (= 격리된 프로세스) |
| VM   | 디스크 통째 (AMI 등)    | VM (= 부팅된 OS)     |


컨테이너가 유행한 이유가 여기 있다. "내 컴퓨터에서는 되는데 서버에서는 안 돼요"는 서버의 런타임 버전·라이브러리 차이 때문이었는데, 이미지는 앱과 환경을 한 덩어리로 묶으니 어디서 실행해도 같다. 쿠버네티스는 레지스트리(Docker Hub, ECR)에서 이미지를 pull 받아 컨테이너로 띄우고, 죽으면 같은 이미지로 다시 만든다. 이미지가 불변이라 몇 번을 다시 만들어도 똑같다.


모니터링에서는 이미지 자체에 지표가 없고, 대신 컨테이너가 어떤 이미지 태그로 떠 있는지(배포 추적), pull 실패(ImagePullBackOff), 이미지 크기(재시작 속도)를 본다.


## 8. kubectl create deployment 하나에서 일어나는 일


```bash
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
```


이 명령 하나로 k8s 내부에서는 이런 체인이 도는다.

1. **kubectl → API Server**: kubectl 이 이 요청을 REST API 호출로 변환해서 kube-apiserver 에 "Deployment 하나 만들어도"라고 전달
2. **API Server → etcd**: API server 가 요청을 검증하고, Deployment 오브젝트를 etcd(k8s 의 상태 저장소)에 기록. 이 시점엔 아직 실제 컨테이너는 없고 "이런 상태여야 한다"는 선언만 저장된 것
3. **Deployment Controller**: control plane 에서 계속 돌고 있는 Deployment 컨트롤러가 etcd 변화를 감지하고, 이 Deployment 를 위한 ReplicaSet 을 하나 생성
4. **ReplicaSet Controller**: ReplicaSet 컨트롤러가 "replica 수(기본 1개)만큼 Pod 가 있어야 한다"는 걸 보고 Pod 오브젝트를 생성. 이 시점에도 Pod 는 아직 "스케줄 안 됨" 상태
5. **Scheduler**: kube-scheduler 가 스케줄 안 된 Pod 을 발견하고, 리소스 여유(CPU/메모리 등)를 보고 어느 노드에 배치할지 결정. minikube 는 노드가 하나뿐이라 사실상 그 노드로 바로 배정
6. **kubelet**: 배정된 노드의 kubelet 이 자기 노드에 할당된 Pod 을 발견하고, container runtime(containerd)에게 kicbase/echo-server:1.0 이미지를 pull 해서 컨테이너를 실행하라고 지시
7. **결과 반영**: 컨테이너가 뜨면 kubelet 이 상태를 API server 에 보고하고, etcd 에 반영되어 kubectl get pods 에서 Running 으로 보임

즉 "선언(Deployment) → 컨트롤러가 원하는 상태를 향해 하위 오브젝트(ReplicaSet→Pod)를 계속 만들어나가는" 게 k8s 의 핵심 동작 방식이다. 이 reconcile loop(원하는 상태 vs 현재 상태를 계속 비교하며 맞춰가는 구조)가 모니터링 시스템에서 "왜 이 상태가 됐는지"를 추적할 때 중요한 개념이다.


이 체인을 계층별로 다시 보면:


```javascript
kubectl (CLI)
  ↓ REST API
kube-apiserver (검증 + 기록)
  ↓
etcd (상태 저장소: "원하는 상태" 저장)
  ↑ 감지
Deployment Controller → ReplicaSet 생성
ReplicaSet Controller → Pod 생성
kube-scheduler → Pod 를 노드에 배치 결정
kubelet(해당 노드) → containerd 에게 이미지 pull + 컨테이너 실행 지시
  ↓
containerd/runc → 실제 커널에 격리 설정, 컨테이너 실행
```


지금까지 배운 개념들이 어디에 들어가는지 정리:

- **이미지**: kicbase/echo-server:1.0 이 레지스트리에서 pull 되는 그 파일시스템 덩어리
- **컨테이너**: containerd 가 runc 를 통해 리눅스 커널에 namespace/cgroup 을 설정해서 만들어낸 격리된 프로세스
- **노드**: kubelet 이 돌고 있는 서버(또는 VM). 이 노드가 IaaS 의 VM 이든 물리 서버든 상관없이 동일하게 동작
- **OS**: containerd/kubelet 이 도는 것 자체가 노드 OS 위의 프로세스

## 9. kubectl expose 는 "포트 노출"이 아니라 Service 생성


```bash
kubectl expose deployment hello-minikube --type=NodePort --port=8080
service/hello-minikube exposed
```


단순히 "포트를 열어준다"가 아니라 **"불안정한 Pod 들 앞에 안정적인 접근 지점(Service)을 하나 만드는"** 작업이다.


**문제: Pod 는 원래 외부에서 접근하기 불안정함**

- Pod 는 언제든 죽었다 다시 뜨 수 있고, 그때마다 내부 IP 가 바뀌는다
- Deployment 로 replica 가 여러 개면 Pod 이 여러 개인데, 클라이언트가 그중 어느 IP 로 접속해야 할지도 모호함

**해결: Service 오브젝트**


kubectl expose 는 실제로 Service 라는 새로운 오브젝트를 하나 만드는 것이다. 이 Service 는:

- Deployment 가 붙인 label 을 기준으로 "이 라벨을 가진 Pod 들"을 자동으로 찾아낸다 (Pod 이 재시작돼서 IP 가 바뀓어도 라벨만 같으면 계속 추적됨)
- 그 Pod 들 앞에 안정적인 가상 IP/포트를 하나 부여하고, 요청이 오면 매칭되는 Pod 들 중 하나로 로드밸런싱해서 전달

**--type=NodePort 의 의미**


Service 자체는 기본적으로 클러스터 내부에서만 접근 가능한 ClusterIP 다. NodePort 로 지정하면 여기에 더해 모든 노드(minikube 노드 포함)의 IP 에 특정 포트(30000~32767 범위)를 열어서, 클러스터 밖에서도 노드IP:NodePort 로 접속하면 이 Service 를 거쳐 Pod 까지 트래픽이 전달되도록 한다.


흐름을 정리하면:


```javascript
외부 요청 → 노드IP:NodePort → Service(가상 IP, 로드밸런싱) → 라벨 매칭되는 Pod → 컨테이너의 8080 포트
```


Service 타입 종류:

- **ClusterIP** (기본값): 클러스터 내부에서만 접근. 다른 Pod 끼리 통신할 때 쓴
- **NodePort**: 각 노드의 포트를 직접 열어 외부 노출. 개발·테스트용으로 많이 쓴
- **LoadBalancer**: 클라우드 제공자의 외부 로드밸런서를 자동으로 연결. EKS/GKE 같은 관리형 환경에서 주로 쓴
- 앞서 본 OpenShift 의 Route 는 NodePort/LoadBalancer 대신 사용하는 OpenShift 고유 외부 노출 방식(Ingress 와 유사)

지금까지 나온 개념과 연결:

- Deployment 는 "원하는 상태(Pod 몇 개)"를 선언하고, Service 는 그 상태가 계속 바끘 때도 "어디로 가야 하는지"를 고정해주는 역할. 둘은 다른 문제를 푸는 서로 보완적인 오브젝트
- 이전에 다룬 "서비스 디스커버리 / 로드밸런싱"이 바로 이 Service 오브젝트의 실제 동작

## 10. Prometheus, API 서버, 에이전트: 모니터링 도구는 어디에 접근하는가


**Prometheus 는 무엇인가**


"지표를 주기적으로 깁어와서 시계열로 저장하고, 질의할 수 있게 해주는 오픈소스 시스템". CNCF(쿠버네티스를 관리하는 재단) 대표 프로젝트로 쿠버네티스 생태계의 사실상 표준.

- 수집(Scrape): 정해둔 대상들의 /metrics HTTP 엔드포인트를 주기적으로 스스로 찾아가서 읽어옵. 대상이 보내주는 게 아니라 Prometheus 가 가지러 감(pull 방식)
- 저장: 읽어온 값에 시간을 찍어 자기 시계열 DB 에 쌓음
- 질의: PromQL 로 "지난 5분간 CPU 평균" 같은 계산을 돌려줘는 질의 언어

Prometheus 자체는 화면이 없고, Grafana 가 그 위에서 그래프를 그린다. Prometheus 는 데이터 창고.


**Prometheus 없이 접근한다면**


Prometheus 는 필수가 아니라 편의 도구다. 안 쓴다면 수집·저장·질의를 직접 만들어야 한다.


```javascript
[Prometheus 쓸 때]
kube-apiserver ─┐
metrics-server ─┤──→ Prometheus (자동 수집+저장) ──→ 내 도구는 여기만 질의
kubelet ──────┘

[Prometheus 안 쓸 때]
kube-apiserver ─┐
metrics-server ─┤──→ 내가 만든 수집기 코드 ──→ 내 DB ──→ 내 도구가 조회
kubelet ──────┘
```


지난 번에 다룬 kubectl proxy 로 API server 직접 두드리기, metrics API 두드리기, ServiceAccount 로 인증하기, 파이썬 클라이언트로 코드 짜기가 전부 "Prometheus 없이 직접 접근하는" 방법이다. 자체 수집·저장 인프라가 이미 있는 온프레미스 모니터링 제품이라면, Prometheus 를 따로 설치하기보다 기존 수집기를 확장해 API 를 직접 두드리는 쪽이 자연스럽다.


**API 서버는 내가 만드는 것이 아니다**


kube-apiserver 는 이미 존재한다. minikube start 를 실행한 순간 이미 떠 있는 쿠버네티스의 핵심 구성 요소. kubectl get pods -n kube-system 을 치면 kube-apiserver 라는 Pod 가 그 안에 이미 돌고 있는 것을 볼 수 있다.


비유: 네이버 API 를 쓰려고 내가 네이버 서버를 만들 필요가 없는 것과 같다. API 서버는 쿠버네티스라는 제품의 일부이고, kubectl 도 Prometheus 도 내가 질 모니터링 코드도 전부 여기에 요청을 보내는 클라이언트일 뿐. 내가 만들어야 하는 건 API 서버가 아니라 API 서버에 요청을 보내고 응답을 처리하는 프로그램이다.


**에이전트란 무엇이고 어디에 어떻게 설치하는가**


에이전트는 감시 대상 에다 직접 심어두는 작은 프로그램이다. 로컬에서 데이터를 모아 중앙으로 보낸다. 클러스터 밖에서 API server 만 두드려서는 몰 정보(노드 디스크 상세, 커널 로그, 컨테이너 내부 파일)가 있어서 노드 안에 직접 들어가 있는 프로그램이 필요하다.


|            | Pull (Prometheus 방식)                | Push (에이전트 방식)       |
| ---------- | ----------------------------------- | -------------------- |
| 누가 먼저 움직이나 | 중앙이 대상을 찾아가 깁음                      | 대상이 중앙으로 알아서 보냄      |
| 대상 쪽에 있는 것 | /metrics 를 열어두는 수동적인 프로그램(exporter) | 능동적으로 전송하는 에이전트      |
| 방화벽 방향     | 중앙 → 대상 (인바운드 허용 필요)                | 대상 → 중앙 (아웃바운드만 필요)  |
| 대표 예       | node-exporter, kube-state-metrics   | 온프레미스 제품의 기존 서버 에이전트 |


폐쇄망 고객사가 많은 온프레미스 제품은 보통 Push 방식이 유리하다. 고객사 방화벽에서 우리 서버로 나가는 건 허용해도, 우리 서버에서 고객사 안으로 들어가는 건 막혀 있는 경우가 흔하기 때문.


에이전트는 서버에 SSH 로 들어가 설치하는 게 아니라, 자체를 컨테이너 이미지로 만들어 쿠버네티스 오브젝트로 배포한다. 이때 Deployment 가 아니라 DaemonSet 을 쓴다.

- Deployment: "이 Pod 를 3개 띄워라" (개수를 내가 지정)
- DaemonSet: "모든 노드에 이 Pod 를 정확히 1개씩 띄워라" (노드가 늘면 자동으로 하나 더 뜨)

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: monitoring-agent
spec:
  selector:
    matchLabels:
      app: monitoring-agent
  template:
    metadata:
      labels:
        app: monitoring-agent
    spec:
      containers:
      - name: agent
        image: monitoring/k8s-agent:1.0
```


설치 순서: (1) 에이전트 프로그램을 컨테이너 이미지로 빌드 → (2) ServiceAccount + ClusterRole 로 API server 읽을 권한 부여 → (3) DaemonSet YAML 을 kubectl apply 로 고객사 클러스터에 배포 → (4) 각 노드의 에이전트가 로컬 kubelet과 API server 를 읽어서 중앙 서버로 push.


**한 줄 정리**


API 서버는 만드는 것이 아니라 이미 있는 것에 요청하는 대상이고, 에이전트는 내가 만들어서 고객사 클러스터 안에 DaemonSet 으로 심는 프로그램이며, Prometheus 는 그 사이에서 수집·저장을 대신 해주는 선택적 도구다.


## 모니터링 관점에서 온 교훈

- 서비스 모델에 따라 볼 수 있는 지표 계층이 다르다. IaaS 는 OS 안에 에이전트를 넣어 전부 보고, PaaS 는 제공자가 열어주는 API 지표만 본다
- 물리 서버 → VM → 컨테이너 → 프로세스로 계층이 늘어나면 같은 "CPU 80%" 라도 어느 계층 숫자인지에 따라 의미가 다르다. VM 은 여유로운데 컨테이너 하나가 cgroup 제한에 걸려 느려지는 상황이 흔하다
- 모니터링 화면의 숫자가 이상하면 서버에 들어가 top 으로 원본 값을 대조하는 게 가장 빠른 확인 방법
