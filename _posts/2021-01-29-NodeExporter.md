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
프로메테우스는 내부적으로 golang을 쓰고있어 쓰레드처리에 뛰어나다. 