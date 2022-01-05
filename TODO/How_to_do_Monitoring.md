
다음과 같은 방식을 취할 수 있을 것 같다.

## 모니터링 정보 제공

case 1

> GPU : [DCGM exporter](https://docs.nvidia.com/datacenter/cloud-native/gpu-telemetry/dcgm-exporter.html)
>
>
> 그 외 : BMC redfish API (혹은 exporter가 적용 가능한지 확인하여 이를 이용)

case 2

> 통합 : [NVSM exporter](https://github.com/NVIDIA/NVSM/tree/master/NVSM-Prometheus)

## 데이터 수집 및 그래픽화

> Prometheus & grafana

## 

## 주의
1. MIG 설정 변경을 위해선 NVSM/DCGM 과 같은 GPU를 모니터링 하는 프로세스를 종료해야함.
2. A100과 같은 MIG 지원 GPU에서 MIG 장치를 모니터링하려면 NVIDIA DCGM v2.0.13 이상 을 사용하는 것이 좋다.