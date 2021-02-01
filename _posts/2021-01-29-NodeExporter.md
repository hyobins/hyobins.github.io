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


Prometheus 의 구조는 이 그림과 같다. <br>
Prometheus는 push 방식을 주로 사용하는 다른 모니터링 도구와 달리, pull 방식을 사용한다. 그래서 서버가 각 클라이언트를 알고 있어야 하는게 아니라 서버에 클라이언트가 떠 있으면 서버가 주기적으로 클라이언트에 접속해서 데이터를 가져오는 방식이다. <br>
구성을 크게 나누면 Exporter, Prometheus Server, Grafana, Alertmanager로 나눌 수 있다.<br>

# Exporter 
Exporter는 서버 구조로 되어있어 모니터링 대상의 Metric 데이터를 수집하고 Prometheus가 접속했을 때 정보를 알려주는 역할을 한다. <br>

Exporter가 타겟으로부터 메트릭을 수집하여 HTTP 엔드포인트를 열어 주면 프로메테우스 서버가 직접 이 엔트포인트에 접근해 metric을 pull하여 TSDB에 메트릭을 저장한다. 이 말은 웹 브라우저 등에서 해당 HTTP 엔드포인트에 접속하면 Metric의 내용을 볼 수 있다는 의미이다.<br>

+ 프로메테우스는 내부적으로 golang을 쓰고있어 쓰레드처리에 뛰어나다. <br>



## 사용법

설치 확인 (Pod, Service)

```bash
[root@platform-group-k8s-1 ~]# kubectl get pod -n iris-cloud -o wide
NAME                                            READY   STATUS    RESTARTS   AGE    IP                
(생략)
prometheus-kube-state-metrics-7d7b59ff4-7xm5q   1/1     Running   0          17d    10.0.(생략)        
prometheus-node-exporter-dl5gl                  1/1     Running   0          104d   <노트 익스포터 IP>
prometheus-server-ff59766b-d9x4r                2/2     Running   0          17d    10.0.(생략)        

[root@platform-group-k8s-1 ~]# kubectl get service -n iris-cloud -o wide
NAME                            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)               SELECTOR
(생략)
metricbeat-kube-state-metrics   ClusterIP   (생략)          <none>        8080/TCP              app.kubernetes.io/instance=metricbeat,app.kubernetes.io/name=kube-state-metrics
prometheus-kube-state-metrics   ClusterIP   (생략)          <none>        8080/TCP              app.kubernetes.io/instance=prometheus,app.kubernetes.io/name=kube-state-metrics
prometheus-node-exporter        ClusterIP   None           <none>        9100/TCP              app=prometheus,component=node-exporter,release=prometheus
prometheus-server               NodePort    (생략)          <none>        80:32021/TCP          app=prometheus,component=server,release=prometheus
```


정보를 알고싶은 node-exporter의 IP주소:9100 으로 curl을 날려 확인해준다. 

```bash
[root@platform-group-k8s-1 ~]# curl http://<노드 익스포터 IP>/metrics
# HELP go_gc_duration_seconds A summary of the pause duration of garbage collection cycles.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 1.7407e-05
go_gc_duration_seconds{quantile="0.25"} 7.1308e-05
go_gc_duration_seconds{quantile="0.5"} 0.000156713
go_gc_duration_seconds{quantile="0.75"} 0.000280957
go_gc_duration_seconds{quantile="1"} 0.011777478
go_gc_duration_seconds_sum 134.375684181
go_gc_duration_seconds_count 347477

이하 중략
```


# Promethus Server






