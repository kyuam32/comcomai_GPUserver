# Before Init

## GPU 서버에서 모니터링 할만한 게 뭐가 있을까?

- CPU, GPU, RAM, Disk R/W, Network 그외 기타 리소스들
    - GPU server 에서 언제 이 리소스들이 사용되고, 어떠한 이슈가 있을 것이라 예상되는가? 그밖에 뭔가 중요한게 있나? 계속 업데이트 해보자.
        - CPU
            - 언제 CPU 사용량이 올라갈까?
                - CPU를 사용한 데이터의 전처리가 필요할 시?
            - ML을 수행할 때 CPU 성능에 따른 GPU 의 병목현상이 발생하는가?
                - 어떤 ML을 돌리는가에 따라 케바케인듯
        - GPU
            - 현재 몇개의 GPU가 있는지 모르겠지만... 2개 이상이라는 가정하에... 어떤 리소스 확인이 필요할 것인가.
                - 전체 사용량
                - 각 GPU 별 사용량
                - MIG 당 사용량 -> MIG 가 어떤 사용자에게 할당됐는지도 확인이 되나?
                - MIG 가 어떻게 나뉘어져 있는지도 모니터링이 되나?
                    
                    -> MIG profile 이 변경될수도 있는가?
                    
            - Nvidia 는 데이터 로딩 라이브러리(DALI)도 제공해준다.(데이터 전처리를 GPU가 처리)
        - RAM
            - I/O 데이터셋에 의한 DRAM 사용량 초과 -> Out of Memory
        - Network
            - 사용자가 Container 에서 작업한 결과물을 외부로 이전
            - 데이터셋이 외부 스토리지에 있는경우
        - Disk
            - 사용자가 Container 를 사용하면서 대용량을 저장해야하는 상황이 있는가? -> 결과가 계산된 모델?같은 경우에 용량이 큰 것 같던데..
                - 사용자의 Input data에 따라 위와 같은 상황이 발생할 수 있을 듯...
            - 동일한 프로그램(아나콘다/주피터 놑북)을 거진 다 사용하는것 같은데... 이를 컨테이너마다 설치하는것이 맞는것인가?
        - 기타
            - Power
            - 온도

---

## 모니터링에 대하여...

1. 사용자에게 주어지는 모든 리소스들은 모니터링의 대상이 될 듯 싶음.
    - 몇 가지 정해진 도커 이미지(리소스 할당량에 따라 이미지 종류를 나누는 것인가?)를 제공하고자 하는 것 같은데... 그렇다면 사용자는 한정된  리소스 환경에서 직접 ML 방식을 최적화를 해야할 것이고... 그럼 사용자는 자신에게 주어진 가용 리소스가 얼마나 되고, 현재 사용량이 어떤지 알고싶지 않을까?
    - 이 정보를 통해 사용자에게 어떠한 인사이트를 전달할수도.
        
        -> ex) 전처리 최적화가 필요하다 / CPU 사용에 비해 GPU 사용이 효율적이지 못하다 / 데이터 파이프라인이 효율적이지 않다
        
2. 운영자 입장에서는 서버 운영에 치명적일 수 있는 리소스 사용 감시가 필요하지 않을까 
    
    -> 이와 같은 케이스는 뭐가 있을까
    

---

## 궁금한 것

~~→ 아래의 궁금증은 NVIDIA officaial document  을 참조하면 대부분 해결이 된다.~~

- GPU 서버의 하드웨어는 어떻게 구성돼있는지?

[DGX Station A100 User Guide](https://docs.nvidia.com/dgx/dgx-station-a100-user-guide/index.html)

---

- 왜 도커를 사용하는것인지? kubespray는 왜?
- `SFTP로 접속시 도커 컨테이너인 것 같은 디렉토리(admin, nvidia, 각 사용자별 디렉토리)가 다 보이는데... Home 하위 디렉토리는 permission denied 뜨긴함. 이게 맞는것인가? nvidia 디렉토리의 경우 key 같은 것도 노출돼 있음.`

 Docker?

일단 NVIDIA는 A100 서버에서 워크로드를 실행하는데 다음과 같은 방법을 제안하기 때문으로 보임.

1. Docker 컨테이너 형태
    
    A100 의 DGX OS 는 GPU를 식별하고 컨테이너에 할당하도록 Docker가 이미 구성돼있음! 이를 사용하는 방법이 가장 간단하다.
    
2. Baremetal 형태

Kubernetes ?

서버를 살펴보다 발견한 NVIDIA 디렉토리와 Kubespray 같은 경우는 NVIDIA DeepOps 에서 제공하는 GPU infrastructure and automation tools 을 사용했기 때문으로 보임.

[https://github.com/NVIDIA/deepops](https://github.com/NVIDIA/deepops)

그럼 쿠버네티스는 왜 사용하는가?

 NVIDIA 가 Kubernetes를 딥 러닝과 관련하여 표준 컨테이너 오케스트레이션 플랫폼 중 하나이기에 MIG 지원의 우선 순위로 두었기 때문.

그럼 Kubernetes 가 뭐가 좋길래?

 Kubernetes 클러스터를 사용하는 컴퓨팅 워크로드는 GPU 파티셔닝과 한 클라이언트가 다른 클라이언트의 작업 또는 일정에 영향을 줄 수 없는 다중 테넌트 사용 사례에서 이점을 얻을 수 있기 때문 사용자간의 격리가 가능함.
 [MIG 사용에 이점이 있다](https://developer.nvidia.com/blog/getting-kubernetes-ready-for-the-a100-gpu-with-multi-instance-gpu/)!

왜 이렇게 NVIDIA 를 주구장창 찾는가 ?  [다음 페이지](https://www.notion.so/about-GPU-server-d8e8c7a83bdd4af493568bf91f2c7701) 로!

---

이 밑의 궁금증에 대한 답 또한! [다음 페이지](https://www.notion.so/about-GPU-server-d8e8c7a83bdd4af493568bf91f2c7701) 로!

- MIG 가 어떤 사용자에게 할당됐는지도 확인이 되나?
    
    → 그 전에 각각 인스턴스가 개별로 할당되는것이 맞는가...?
    
- MIG profile 이 필요에 따라 동적으로 정의될 수 있는가?
    
    →  민호님 답변 : 계획에 없음. 추가적으로 MIG prifile을 변경하고자 하면 해당 GPU를 아무도 사용하지 않고 있어야함
    
    →  NVIDIA DOC : 
    

![MIG profile table](Before%20Init%20edc61e325532417394786a10ca1dd37e/MIG_profile.png)

MIG profile table

- MIG profile 구성에 따라 Docker 이미지를 매핑해야 하는 것인가?
- 컨테이너마다 동일한 프로그램(아나콘다/주피터 놑북)을 설치하는것이 맞는것인가?
- GPU 의 경우 기술적인 한계에 사용자의 필요에따라 동적으로 Scaling 할 수 없다면... 그 외의 리소스는 가능한가? -> 이 부분이 도커 이미지 별로 나누어서 해결이 되는건가?

---

## 그 외 필요해 보이는 것

1. 초기 사용자를 위한 server Infra 메뉴얼이 좀 필요해 보임...
    
    → 내가 써보자.
    
2. 사용자가 ssh를 통해 접속하고, GPU 리소스를 사용하는 일련의 과정 역시 메뉴얼이 필요해 보임