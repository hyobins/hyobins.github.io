---
layout: post
published: On
title: Node Exporter | 사용법
category: nodeexporter
subtitle: Node Exporter 사용방법
date: '2021-03-10'
---

## Local

1. Building Binaries
   
```sh
[root@k8s-dev node_exporter]# make build
>> building binaries
GO111MODULE=on /home/hb0617/go/bin/promu --config .promu.yml build --prefix /home/hb0617/workspace/node_exporter
 >   node_exporter
```

2. binary 실행 

```sh
[root@k8s-dev node_exporter]# ./node_exporter
level=info ts=2021-03-10T02:00:17.734Z caller=node_exporter.go:178 msg="Starting node_exporter" version="(version=1.1.1, branch=main, revision=36c80e2fec9b0d3d17102df0a8b8aeb226a4c409)"
level=info ts=2021-03-10T02:00:17.735Z caller=node_exporter.go:179 msg="Build context" build_context="(go=go1.14, user=root@k8s-dev, date=20210310-01:57:38)"
level=warn ts=2021-03-10T02:00:17.735Z caller=node_exporter.go:181 msg="Node Exporter is running as root user. This exporter is designed to run as unpriviledged user, root is not required."
level=info ts=2021-03-10T02:00:17.736Z caller=filesystem_common.go:74 collector=filesystem msg="Parsed flag --collector.filesystem.ignored-mount-points" flag=^/(dev|proc|sys|var/lib/docker/.+)($|/)
[생략]
```

3. 실행중인 http 서버로 curl 요청 날려서 확인

특정 metric을 보려면 "grep | {metric 이름}" 옵션을 사용해주면 된다.
   
```sh
[root@k8s-dev ~]# curl localhost:9100/metrics | grep container
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
# HELP node_container_cpu_cstime Current CPU cstime by container.
# TYPE node_container_cpu_cstime gauge
node_container_cpu_cstime{id="1ed5b3796a3c3c56c1398ba2c0577e2e91b7e043a6cea9ed78c97413cba8c866",name="/nginx1g",pid="319",type="cstime"} 0
node_container_cpu_cstime{id="1ed5b3796a3c3c56c1398ba2c0577e2e91b7e043a6cea9ed78c97413cba8c866",name="/nginx1g",pid="320",type="cstime"} 0
node_container_cpu_cstime{id="1ed5b3796a3c3c56c1398ba2c0577e2e91b7e043a6cea9ed78c97413cba8c866",name="/nginx1g",pid="321",type="cstime"} 0
node_container_cpu_cstime{id="1ed5b3796a3c3c56c1398ba2c0577e2e91b7e043a6cea9ed78c97413cba8c866",name="/nginx1g",pid="322",type="cstime"} 0
node_container_cpu_cstime{id="1ed5b3796a3c3c56c1398ba2c0577e2e91b7e043a6cea9ed78c97413cba8c866",name="/nginx1g",pid="323",type="cstime"} 0
node_container_cpu_cstime{id="1ed5b3796a3c3c56c1398ba2c0577e2e91b7e043a6cea9ed78c97413cba8c866",name="/nginx1g",pid="324",type="cstime"} 0
node_container_cpu_cstime{id="1ed5b3796a3c3c56c1398ba2c0577e2e91b7e043a6cea9ed78c97413cba8c866",name="/nginx1g",pid="325",type="cstime"} 0
[생략]
```

<br>

## k8s-pod으로 띄워서 실행해보기 
