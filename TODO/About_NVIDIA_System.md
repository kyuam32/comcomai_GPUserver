# 42seoul 의 GPU server 에 대해 알아보자

4줄 요약 
1. 42서울에서 사용하는 GPU 서버는 [DGX Station A100](https://docs.nvidia.com/dgx/dgx-station-a100-user-guide/index.html) 이다
2. DGX Station A100는 단순히 하드웨어 덩어리가 아니라 Nvidia 가 제공하는 GPU server 통합 솔루션이라고 볼 수 있다.
3. 최고의 document 는 제조사가 제공해주고 있다! 기초부터 시작해 더 깊고 자세한 내용을 알고 싶다면 [Nvidia developer](https://developer.nvidia.com/) 로 가보자! 모든 것이 있다! 

아래의 내용에 대해 잘못된 내용이 있다면! 가감없는 피드백 부탁드립니다!

### GPU 서버의 등장 배경
[Nvidia 의 CPU vs GPU 를 설명하는 정말 간단한 영상](https://www.youtube.com/watch?v=-P28LKWTzrI)
* CPU는 `광범위한 작업을 순차적으로 빠르게 처리하도록` 설계됨 ( 다목적성을 띄고있다 )
* GPU는 `작은 작업 여러개를 동시에 처리하도록` 설계됨 ( GPU의 등장 배경인 고해상도 이미지와 비디오등을 처리하기 위해 이와 같은 특징을 띔 )
  
  -> 이말인 즉 GPU는 대규모 데이터 셋에 대해 병렬작업에 최적화 됐다는 것. 

  -> 그러면 꼭 GPU를 그래픽 렌더링에만 써야하나? 대규모 병렬연산이 필요한 다른 작업(general purpose computing)도 좋아보이는데!

  -> Nvidia : 오케이 우리가 제공해줄게 CUDA !

  -> 

### CUDA (Computed Unified Device Architecture)

CUDA는 Nvidia 가 제공하는 GPU toolkit !

[Nvidia 는 이를 다음과 같이 설명한다](https://blogs.nvidia.com/blog/2012/09/10/what-is-cuda-2/) 

> CUDA is a parallel computing platform and programming model that makes using a GPU for general purpose computing simple and elegant

그래픽 연산을 위해 사용하던 GPU 를 `general purpose computing` 목적으로 사용 가능하게 해주는 것이다!

CUDA toolkit 의 일부인 [NVCC](https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html)(Nvidia CUDA Compiler) 가 cuda 소스파일을 CPU 와 GPU 에서 수행할 명령을 나누고, GPU 에서 병렬 연산을 할 수 있도록 컴파일 해준다.

[관련문서 1](https://blogs.nvidia.com/blog/2012/09/10/what-is-cuda-2/)

~~AMD 는...?~~

### NVIDIA DGX Station™ A100

Nvidia 의 2020년 Tensor Core GPU를 탑제한 워크스테이션.

생긴것을 보면 데스크탑 처럼 보여서 아~ 하드웨어가 엄청 좋은 데스크탑인가? 싶기도 하네요 ( 저는 처음에 이렇게 받아들였습니다 )

하지만 실제로는 `Data center technology without a data center or additional IT investment` 작은 데이터센터이자 GPU server 를 위한 통합 솔루션입니다.

정리하면

> 42서울이 NVIDIA DGX Station™ A100 를 사용한다는 것은 단순히 하드웨어를 구매했다기 보다 GPU server를 운영하는 통합 솔루션을 구매했다고 보시면 됩니다. 
>
> 본 제품을 사용할 시 클라우드와 데이터 센터에 이르기까지 필요한 Software 솔루션까지 Nvidia에서 지원이 되구요!
> 
> 이는 서버 운여에 필요한 기술은 모두 Nvidia에서 지원해준다 이지만, 어떻게보면 Nvidia 에 상당히 의존적으로 보이기도 하네요!
> 
> 자 그럼 우리에게 필요한 System Monitoring 도 데이터센터를 운영할때 꼭 필요한 부분이겠죠? 
> 
> 뭔가 NVIDIA 가 이에 대한 솔루션을 제공하지 않을까! 싶은 생각이 드실겁니다

# Hardware (업데이트 예정!)

![img.png](hardware_spec.png)

NVIDIA A100 TENSOR Core GPU 가 4대 장착돼 있습니다.

여기엔 나와있진 않지만 워크스테이션을 직접 모니터에 연결해서 사용하기 위한 DGX Display GPU 도 하나 더 있다고 하네요.

하드웨어적 특징을 하나씩 살펴봅시다

[관련문서 1](https://developer.download.nvidia.com/video/gputechconf/gtc/2020/presentations/s21730-inside-the-nvidia-ampere-architecture.pdf?t=eyJscyI6ImdzZW8iLCJsc2QiOiJodHRwczpcL1wvd3d3Lmdvb2dsZS5jb21cLyJ9)

### NVIDIA A100 TENSOR Core GPU (업데이트 예정!)

![img.png](A100_tensor_gpu.png)

[간략한 자료](https://developer.download.nvidia.com/video/gputechconf/gtc/2020/presentations/s21730-inside-the-nvidia-ampere-architecture.pdf?t=eyJscyI6ImdzZW8iLCJsc2QiOiJodHRwczpcL1wvd3d3Lmdvb2dsZS5jb21cLyJ9)

Nvidia 에서 제공하는 A100 TENSOR Core GPU 의 레이아웃입니다!

* MIG (Multi-Instance GPU) (업데이트 예정!)

[관련문서 1](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/TB-10226-001_v01.pdf)
[관련문서 2](https://docs.nvidia.com/datacenter/tesla/mig-user-guide/)

# Software (업데이트 예정!)

## [OS](https://docs.nvidia.com/dgx/dgx-os-5-user-guide/index.html#network-config)

ubuntu 기반의 자체 배포판을 사용

[관련 문서 1](https://docs.nvidia.com/dgx/dgx-os-5-user-guide/index.html#network-config)

## 모니터링 관련

### **BMC (baseboard management controller)**

[관련문서 1](https://docs.nvidia.com/dgx/dgx-station-a100-user-guide/index.html#using-bmc)

BMC 는 전문화된 작은 프로세서 칩으로, 서버 하드웨어 관리를 위해 만들어졌다. 

이는 내장된 그래픽과 제어 로직을 가진 System on Chip으로, 일반적으로 모니터링할 장치의 메인보드 또는 마더 보드에 포함되어 있다.

하드웨어 상태(ex. FAN 속도, 온도, 전압 등)를 모니터링하여 제공해주고 redfish API 를 통해 이를 조회하거나, 부팅 순서, 재부팅, 전원 임계값 과 같은 설정을 변경할 수도 있다!

* redfish API : Redfish는 DMTF(SPMF) 그룹에서 개발한 JSON 스키마 또는 OData를 활용하는 스케일 아웃 서버 관리를 위한 표준 및 RESTful API

Table 1. 은 BMC로부터 제공되는 정보들!

![img.png](BMC.png)

### **[NVSM (NVIDIA System Management)](https://docs.nvidia.com/datacenter/nvsm/21.07/nvsm-user-guide/index.html#topic_3_4_5)**

NVIDIA® 시스템 관리(NVSM)는 NVIDIA에서 설계한 서비스를 관리하고 모니터링하기 위한 소프트웨어 스택이다!

NVSM은 "Always on" 모니터링 엔진으로 GPU 뿐만 아니라 시스템 전반의 리소스들을 모니터링 할 수 있다.

또한 제공되는 API 를 통해 경고 모니터링에대한 정보를 확인하거나 RAID 재구축, 펌웨어 업데이트 같은 기능도 수행가능하다.([API reference](https://docs.nvidia.com/datacenter/nvsm/19.06/nvsm-api/index.html))

* RAID : 논리적으로 여러 디스크를 단일 어레이에 함께 넣는 방법입


`nvsm_api_gateway` DGX OS 이미지의 일부이며 DGX가 부팅될 때 systemd에 의해 시작됩니다

![img.png](NVSM_arch.png)

### [DCGM ( Data Center GPU Manager )](https://docs.nvidia.com/datacenter/dcgm/latest/dcgm-user-guide/overview.html#overview)

NVIDIA® 데이터 센터 GPU 관리자 (DCGM)는 클러스터 및 데이터 센터 환경에서 NVIDIA Tesla GPU의 관리를 단순화합니다.

기본적으로 DCGM은 각 호스트 시스템에서 다양한 기능을 수행하는 지능적이고 가벼운 사용자 공간 라이브러리/에이전트입니다.




