---
layout: post
published: On
title: Node Exporter
subtitle: Prometheus/Node Exporter를 이용한 모니터링
date: '2021-01-29'
---

# Prometheus
Prometheus는 오픈소스 모니터링 툴이다.

<img src="https://miro.medium.com/max/1400/0*_EqEmeXfdivLrtTu.png">



Exporter는 서버 구조로 되어있어 다양한 모니터링 대상에 대한 무한한 확장이 가능하다. <br>
Exporter가 타겟으로부터 메트릭을 수집하여 HTTP 엔드포인트를 열어 프로메테우스가 수집해갈 수 있도록 하면 프로메테우스 서버가 직접 이 엔트포인트에 접근해 metric을 pull하여 TSDB에 메트릭을 저장한다. <br>
서버의 개수가 정해져 있다면 프로메테우스에서 모니터링 대상을 관리하는데 어려움이 없지만, 오토스케일링이 많이 사용되는 클라우드 환경이나 쿠버네티스 클러스터에서는 모니터링 대상의 IP가 동적으로 변경되기 때문에 이를 일일이 설정파일에 넣는데 한계가 있다. <br>
이러한 문제를 해결하기 위해 프로메테우스는 DNS나 Consul, etcd와 같은 다양한 서비스 디스커버리 서비스와 연동을 통해 모니터링 목록을 가지고 모니터링을 수행한다.<br><br>

+ 프로메테우스는 내부적으로 golang을 쓰고있어 쓰레드처리에 뛰어나다. <br>


<br><br>

# Exporter(node-exporter)

모니터링 대상이 프로메테우스의 데이터 포맷을 지원하지 않는 경ㅇ에는 별도의 에이전트를 설치해야 지표를 얻어올 수 있는데 이 에이전트를 Exporter 라고 한다.