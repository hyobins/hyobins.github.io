---
layout: post
published: On
title: Prometheus | TSDB
category: monitoring
subtitle: 모니터링을 위한 시계열 데이터베이스 TSDB 
date: '2021-03-15'
---

# OpenTSDB란?

### OpenTSDB란 Hbase 기반의 Open source Time Series Database 이다. <br>

> [Github 코드](https://github.com/OpenTSDB/opentsdb) <br>

Open TSDB는 컴퓨터 시스템(OS, Network, appliaction 등 )에서 수집된 metrics들을 store, index and serve 하기 위해 만들어졌다. <br>
TSDB는 Row 기반인 관계형 데이터페이스(RDB)와 달리 Column 기반인 NoSQL Database이다. RDB와 달리 스키마가 없으므로 읽고 쓰기가 빠르고 운영 중 서버의 확장이 가능하다.<br>

또한 Hbase의 확장성 덕분에 OpenTSDB는 수만 개의 호스트 및 응용 프로그램에서 수천개의 metric을 고속으로 매 초마다 수집할 수 있다. <br>이러한 대규모 데이터에 쉽게 접근할 수 있고 도식화 하기 편리하도록 만들어 졌기 때문에 모니터링에 사용하기 좋다. 

<br>

## 작동원리

<img src="../assets/img/tsdb-architecture.png">

수집 대상인 server에 설치된 collector 클라이언트가 TSD(Time Series Daemon)서버로 전송하면 TSD가 Hbase에 저장한다.
OpenTSDB는 http api, web ui, telnet을 통한 읽기/쓰기를 지원한다.

<br>

## 기본적인 데이터 포맷
- Metric name 
- Unix Timestamp(Epoch)
- a Value(int64, float, JSON)
- A set of tags

<br>

# 사용법

OpenTSDB도 production level에서 사용하려면 HBase 설정이 필요하다. <br>
> [Hbase 설정법](http://engineering.vcnc.co.kr/2013/04/hbase-configuration/)

<br>
여기서는 개발 및 학습 목적으로서 docker 컨테이너로 띄워 보자. <br>
docker hub에서 open tsdb를 검색하면 가장 많이 사용되는 container를 다운받아서 실행할 수 있다. 

> [링크](https://hub.docker.com/r/petergrace/opentsdb-docker/)


```sh
[root@k8s-dev ]# docker pull petergrace/opentsdb-docker
Using default tag: latest
latest: Pulling from petergrace/opentsdb-docker
188c0c94c7c5: Pull complete
0482a7a172c0: Pull complete
1838cd58688f: Pull complete
...
b7313d7365bd: Pull complete
Digest: sha256:05d396f19800260c9bcfc918dbab97c289cf5f94cd90abed03adb357244516f0
Status: Downloaded newer image for petergrace/opentsdb-docker:latest
docker.io/petergrace/opentsdb-docker:latest
[root@k8s-dev ]# docker run -dp 4242:4242 petergrace/opentsdb-docker
85b619f7c112f80e615e4abf86ede8ae38276d5101d9c8e28462464e8adc8dce
```

