---
layout: post
published: On
title: [Docker] Docker API의 go client
category: docker
subtitle: golang으로 docekr api 호출하여 다양한 정보 얻기
date: '2021-01-26'
---

# Docker API 

Docekr는 Docker daemon과 정보를 주고받기위한 api(=Docker Engine API)를 제공한다. <br>

예시는 [이곳](https://docs.docker.com/engine/api/sdk/examples/) 에서 확인할 수 있다.

<br>

docker ps 와 같은 출력값을 갖는 예시이다. 

```go
package main

import (
	"context"
	"fmt"

	"github.com/docker/docker/api/types"
	"github.com/docker/docker/client"
)

func main() {
    //Docker Daemon에 연결부분
	ctx := context.Background()
	cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
	if err != nil {
		panic(err)
	}

    //Docker api 호출하여 사용
	containers, err := cli.ContainerList(ctx, types.ContainerListOptions{})
	if err != nil {
		panic(err)
	}

    //결과값 출력
	for _, container := range containers {
		fmt.Println(container.ID)
	}
}
```


이 예시에 활용된 ContainerList가 api 인데, Go로 작성되어 활용할 수 있는 api list는 [이곳](https://pkg.go.dev/github.com/docker/docker/client) 에서 확인할 수 있다.

<br><br>

## 컨테이너 정보를 출력해주는 api/결과값 정리

아래 예시들은 go routine을 활용하였으므로 코드내에 waitGroup을 이용하여 wait 처리를 해주어야 한다. <br><br>


__func (*Client) CheckpointList__<br>
특정 컨테이너의 checkpointlist return 

```go
go func() {
    defer w.Done()
    var checkpoints []types.Checkpoint
    options := types.CheckpointListOptions{}
    checkpoints, err := cli.CheckpointList(ctx, "7e03a3c395de", options)
    if err != nil {
        panic(err)
    }
    doc, _ := json.MarshalIndent(checkpoints, "", "    ")
    fmt.Println(string(doc))
}()
```

이 예시는 "experimental": true 옵션을 추가해주어야 한다.

```bash
null
```

<br><br>

__func (*Client) ClientVersion__<br>
client가 사용하고있는 api version return

```go
go func() {
    defer w.Done()
    var clientversion string
    clientversion = cli.ClientVersion()

    doc, _ := json.MarshalIndent(clientversion, "", "    ")
    fmt.Println(string(doc))
}()
```

```bash
"1.41"
```

<br><br>

__func (*Client) ContainerDiff__<br>
container가 시작된 이후 filesystem의 변화

```go
go func() {
    defer w.Done()
    var changes []container.ContainerChangeResponseItem
    changes, err := cli.ContainerDiff(ctx, "f425bc67365a")
    if err != nil {
        panic(err)
    }

    doc, _ := json.MarshalIndent(changes, "", "	")
    fmt.Println(string(doc))
}()
```

```bash
[
	{
		"Kind": 0,
		"Path": "/var"
	},
	{
		"Kind": 0,
		"Path": "/var/lib"
	},
	{
		"Kind": 1,
		"Path": "/var/lib/registry"
	}
]
```

<br><br>

__func (*Client) ContainerExecInspect__<br>
information about a specific exec process on the docker host

```go
go func() {
    defer w.Done()
    var resp types.ContainerExecInspect
    resp, err := cli.ContainerExecInspect(ctx, "6f5960472a04efba239e223f31fa84c99145db430a20321cd36f3bfcbd4f1b66")
    if err != nil {
        panic(err)
    }

    doc, _ := json.MarshalIndent(resp, "", "	")
    fmt.Println(string(doc))
}()
```

```bash
{
	"ID": "6f5960472a04efba239e223f31fa84c99145db430a20321cd36f3bfcbd4f1b66",
	"ContainerID": "2e24db742d5620139d27b3ce2c4e853da951d74e92682d14fdb5e7bda41ca760",
	"Running": false,
	"ExitCode": 0,
	"Pid": 1942
}
```

<br><br>

__func (*Client) ContainerInspect__<br>
container information 리턴 

```go
go func() {
    defer w.Done()
    resp, err := cli.ContainerInspect(ctx, "2e24db742d56")
    if err != nil {
        panic(err)
    }
    doc, _ := json.MarshalIndent(resp, "", "	")
    fmt.Println(string(doc))
}()
```

```bash
{
	"Id": "2e24db742d5620139d27b3ce2c4e853da951d74e92682d14fdb5e7bda41ca760",
	"Created": "2021-01-26T02:13:20.4844572Z",
	"Path": "/workspace/test/test1",
	"Args": [
		"/workspace/test/test1"
	],
	"State": {
		"Status": "running",
		"Running": true,
		"Paused": false,
		"Restarting": false,
		"OOMKilled": false,
		"Dead": false,
		"Pid": 1770,
		"ExitCode": 0,
		"Error": "",
		"StartedAt": "2021-01-26T02:13:20.9205834Z",
		"FinishedAt": "0001-01-01T00:00:00Z"
	},
	"Image": "sha256:380a1ca228eee322793dacb23effc50de46f9a1cc289979b83cfc4fc7f0fb31a",
	"ResolvConfPath": "/var/lib/docker/containers/2e24db742d5620139d27b3ce2c4e853da951d74e92682d14fdb5e7bda41ca760/resolv.conf",
	"HostnamePath": "/var/lib/docker/containers/2e24db742d5620139d27b3ce2c4e853da951d74e92682d14fdb5e7bda41ca760/hostname",
	"HostsPath": "/var/lib/docker/containers/2e24db742d5620139d27b3ce2c4e853da951d74e92682d14fdb5e7bda41ca760/hosts",
	"LogPath": "/var/lib/docker/containers/2e24db742d5620139d27b3ce2c4e853da951d74e92682d14fdb5e7bda41ca760/2e24db742d5620139d27b3ce2c4e853da951d74e92682d14fdb5e7bda41ca760-json.log",
	"Name": "/condescending_shamir",
	"RestartCount": 0,
	"Driver": "overlay2",
	"Platform": "linux",
	"MountLabel": "",
	"ProcessLabel": "",
	"AppArmorProfile": "",
	"ExecIDs": null,
	"HostConfig": {
		"Binds": null,
		"ContainerIDFile": "",
		"LogConfig": {
			"Type": "json-file",
			"Config": {}
		},
		"NetworkMode": "default",
		"PortBindings": {},
		"RestartPolicy": {
			"Name": "no",
			"MaximumRetryCount": 0
		},
		"AutoRemove": false,
		"VolumeDriver": "",
		"VolumesFrom": null,
		"CapAdd": null,
		"CapDrop": null,
		"CgroupnsMode": "host",
		"Dns": [],
		"DnsOptions": [],
		"DnsSearch": [],
		"ExtraHosts": null,
		"GroupAdd": null,
		"IpcMode": "private",
		"Cgroup": "",
		"Links": null,
		"OomScoreAdj": 0,
		"PidMode": "",
		"Privileged": false,
		"PublishAllPorts": false,
		"ReadonlyRootfs": false,
		"SecurityOpt": null,
		"UTSMode": "",
		"UsernsMode": "",
		"ShmSize": 67108864,
		"Runtime": "runc",
		"ConsoleSize": [
			0,
			0
		],
		"Isolation": "",
		"CpuShares": 0,
		"Memory": 0,
		"NanoCpus": 0,
		"CgroupParent": "",
		"BlkioWeight": 0,
		"BlkioWeightDevice": [],
		"BlkioDeviceReadBps": null,
		"BlkioDeviceWriteBps": null,
		"BlkioDeviceReadIOps": null,
		"BlkioDeviceWriteIOps": null,
		"CpuPeriod": 0,
		"CpuQuota": 0,
		"CpuRealtimePeriod": 0,
		"CpuRealtimeRuntime": 0,
		"CpusetCpus": "",
		"CpusetMems": "",
		"Devices": [],
		"DeviceCgroupRules": null,
		"DeviceRequests": null,
		"KernelMemory": 0,
		"KernelMemoryTCP": 0,
		"MemoryReservation": 0,
		"MemorySwap": 0,
		"MemorySwappiness": null,
		"OomKillDisable": false,
		"PidsLimit": null,
		"Ulimits": null,
		"CpuCount": 0,
		"CpuPercent": 0,
		"IOMaximumIOps": 0,
		"IOMaximumBandwidth": 0,
		"MaskedPaths": [
			"/proc/asound",
			"/proc/acpi",
			"/proc/kcore",
			"/proc/keys",
			"/proc/latency_stats",
			"/proc/timer_list",
			"/proc/timer_stats",
			"/proc/sched_debug",
			"/proc/scsi",
			"/sys/firmware"
		],
		"ReadonlyPaths": [
			"/proc/bus",
			"/proc/fs",
			"/proc/irq",
			"/proc/sys",
			"/proc/sysrq-trigger"
		]
	},
	"GraphDriver": {
		"Data": {
			"LowerDir": "/var/lib/docker/overlay2/fddf2cfcb626e2aab83e955304b3b4d429dcbac546d2ca0419167943f50f111b-init/diff:/var/lib/docker/overlay2/bdfc6d75105c5ac4a30199f57c0e8445f374b98f9398813d8df44003ebe522d9/diff:/var/lib/docker/overlay2/cb36e0f8bfedfad4c3490a901c9b21ddafedc1c55225c2ebb08376682c4f8dcb/diff:/var/lib/docker/overlay2/6382b880a5fd5198c424592fe541cb0ba3bd3f2f72709428d602391dd6a0363e/diff:/var/lib/docker/overlay2/c7af2fb1f7c5d662de87283b27db300f816599497539e9fef6ed5f3cca1c3748/diff:/var/lib/docker/overlay2/5ebc8c70b7090e6ccf7e8bab25e77a1de0d37eb7f7816ffae0b6966bd14a1383/diff:/var/lib/docker/overlay2/a8838d143a6c5382888a48e24d4bbdff229e3afbabf22b6e0de98e7b2bbf56ff/diff:/var/lib/docker/overlay2/b9c15c6d5c38873c8e44533a18e908f2f4e5c8eb07849438e66247a5b27b8fbf/diff:/var/lib/docker/overlay2/900fb1f5d2f45134970b8933563e52ff60233d54eea21dc9bd7d11db497a2362/diff:/var/lib/docker/overlay2/fc454ea645e664c88beeca6530b29178d50cc666cbdbbdd98aca90d5b8ffd7d5/diff",
			"MergedDir": "/var/lib/docker/overlay2/fddf2cfcb626e2aab83e955304b3b4d429dcbac546d2ca0419167943f50f111b/merged",
			"UpperDir": "/var/lib/docker/overlay2/fddf2cfcb626e2aab83e955304b3b4d429dcbac546d2ca0419167943f50f111b/diff",
			"WorkDir": "/var/lib/docker/overlay2/fddf2cfcb626e2aab83e955304b3b4d429dcbac546d2ca0419167943f50f111b/work"
		},
		"Name": "overlay2"
	},
	"Mounts": [],
	"Config": {
		"Hostname": "2e24db742d56",
		"Domainname": "",
		"User": "",
		"AttachStdin": false,
		"AttachStdout": true,
		"AttachStderr": true,
		"Tty": false,
		"OpenStdin": false,
		"StdinOnce": false,
		"Env": [
			"PATH=/go/bin:/usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
			"GOLANG_VERSION=1.15.6",
			"GOPATH=/go"
		],
		"Cmd": [
			"/workspace/test/test1"
		],
		"Image": "repo.iris.tools/hb/hb-image:v1.0",
		"Volumes": null,
		"WorkingDir": "/workspace/test",
		"Entrypoint": [
			"/workspace/test/test1"
		],
		"OnBuild": null,
		"Labels": {}
	},
	"NetworkSettings": {
		"Bridge": "",
		"SandboxID": "9e1c8d2c1d0cd2c308f26c8a0b881a1f5dc21e9a9aa0618c38ebfc5d8da51071",
		"HairpinMode": false,
		"LinkLocalIPv6Address": "",
		"LinkLocalIPv6PrefixLen": 0,
		"Ports": {},
		"SandboxKey": "/var/run/docker/netns/9e1c8d2c1d0c",
		"SecondaryIPAddresses": null,
		"SecondaryIPv6Addresses": null,
		"EndpointID": "ef30136834611a3c5ba3339f1414cdee10a330c470fce29ba8bc5d7277f4aaf9",
		"Gateway": "172.17.0.1",
		"GlobalIPv6Address": "",
		"GlobalIPv6PrefixLen": 0,
		"IPAddress": "172.17.0.2",
		"IPPrefixLen": 16,
		"IPv6Gateway": "",
		"MacAddress": "02:42:ac:11:00:02",
		"Networks": {
			"bridge": {
				"IPAMConfig": null,
				"Links": null,
				"Aliases": null,
				"NetworkID": "ccb81aa17290271669251fe0c635aa3cef5746c30397c6f20c261f5b338c4623",
				"EndpointID": "ef30136834611a3c5ba3339f1414cdee10a330c470fce29ba8bc5d7277f4aaf9",
				"Gateway": "172.17.0.1",
				"IPAddress": "172.17.0.2",
				"IPPrefixLen": 16,
				"IPv6Gateway": "",
				"GlobalIPv6Address": "",
				"GlobalIPv6PrefixLen": 0,
				"MacAddress": "02:42:ac:11:00:02",
				"DriverOpts": null
			}
		}
	}
}
```

<br><br>

__func (*Client) ContainerList__<br>
docker ps 와 동일

```go
go func() {
    defer w.Done()
    containers, err := cli.ContainerList(ctx, types.ContainerListOptions{})
    for _, container := range containers {
        fmt.Println(container.ID)
    }
    if err != nil {
        panic(err)
    }
}()
```

```bash
2e24db742d5620139d27b3ce2c4e853da951d74e92682d14fdb5e7bda41ca760
```

<br><br>

__func (*Client) ContainerLogs__<br>
특정 컨테이너 로그 출력

```go
go func() {
    defer w.Done()
    options := types.ContainerLogsOptions{ShowStdout: true}
    out, err := cli.ContainerLogs(ctx, "7e03a3c395de", options)
    io.Copy(os.Stdout, out)
    if err != nil {
        panic(err)
    }
}()
```

```bash

   ____    __
  / __/___/ /  ___
 / _// __/ _ \/ _ \
 /___/\__/_//_/\___/ v3.3.10-dev
.High performance, minimalist Go web framework
https://echo.labstack.com
.____________________________________O/_______
.                                    O\
%⇨ http server started on [::]:1322
```

<br><br>


__func (*Client) DaemonHost__<br>
host address used by the client 리턴

```go
go func() {
    defer w.Done()
    d := cli.DaemonHost()
    doc, _ := json.MarshalIndent(d, "", "	")
    fmt.Println(string(doc))
}()
```

```bash
"unix:///var/run/docker.sock"
```


<br><br>



__func (*Client) DiskUsage__<br>
current data usage from the daemon 리턴

```go
go func() {
    defer w.Done()
    var du types.DiskUsage
    du, err := cli.DiskUsage(ctx)
    if err != nil {
        panic(err)
    }
    doc, _ := json.MarshalIndent(du, "", "    ")
    fmt.Println("-------------------------------------------\n[disk usage]")
    fmt.Println(string(doc))
}()
```

```bash
"LayersSize": 15734442234,
"Images": [
        {
            "Containers": 0,
            "Created": 1611553539,
            "Id": "sha256:95c13b55df1a35f9a2fe6fa6251031e26ce719c7f43d02147f037cf4d798b22f",
            "Labels": {
                "architecture": "x86_64",
                "io.k8s.description": "IRIS Cloud creates, configures and helps manage K8s Clusters and IRIS installations on Kubernetes",
                "io.k8s.display-name": "IRIS Cloud",
                "name": "IRIS Cloud",
                "url": "http://www.mobigen.com",
                "vendor": "Mobigen"
            },
            "ParentId": "sha256:faf0929450e95c217bdb135dedac32d1ca646b6b02ce4d5211f6e9f1027aa402",
            "RepoDigests": [
                "repo.iris.tools/iris-cloud/iris-cloud@sha256:67483475713e40f62e802ea770b4d46883e8d4b00563369d63b70b537edf5b98"
            ],
            "RepoTags": [
                "repo.iris.tools/iris-cloud/iris-cloud:v0.35.0-dev"
            ],
            "SharedSize": 173140144,
            "Size": 339120350,
            "VirtualSize": 339120350
        },
        {
            현재 저장되어있는 이미지 만큼 출력
        },
"Containers": [
        {
            "Id": "f2a2fa02308be17c6a2d19ae8c68cae493c0ccc99d9e344948dc19af0a984676",
            "Names": [
                "/iris-cloud-mariadb"
            ],
            "Image": "mariadb:10.5",
            "ImageID": "sha256:ade39f0469a3fa0e5f907e4dd7861a4e77573b0757bd4055a7b4c52baea58590",
            "Command": "docker-entrypoint.sh --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --lower_case_table_names=1",
            "Created": 1611314912,
            "Ports": [
                {
                    "IP": "0.0.0.0",
                    "PrivatePort": 3306,
                    "PublicPort": 3306,
                    "Type": "tcp"
                }
            ],
            "SizeRw": 2035,
            "SizeRootFs": 406527507,
            "Labels": {},
            "State": "running",
            "Status": "Up 3 days",
            "HostConfig": {
                "NetworkMode": "default"
            },
            "NetworkSettings": {
                "Networks": {
                    "bridge": {
                        "IPAMConfig": null,
                        "Links": null,
                        "Aliases": null,
                        "NetworkID": "9b19171a2161ae3d409f0ec61f3fc1a24727e0295338622859751093ea5e6043",
                        "EndpointID": "9102e3c93200944fe6f0fca7b676350ec52b5b43caecb6a6d4a1d17365192262",
                        "Gateway": "172.17.0.1",
                        "IPAddress": "172.17.0.2",
                        "IPPrefixLen": 16,
                        "IPv6Gateway": "",
                        "GlobalIPv6Address": "",
                        "GlobalIPv6PrefixLen": 0,
                        "MacAddress": "02:42:ac:11:00:02",
                        "DriverOpts": null
                    }
                }
            },
            "Mounts": [
                {
                    "Type": "bind",
                    "Source": "/root/stats/mariadb/init-sql",
                    "Destination": "/docker-entrypoint-initdb.d",
                    "Mode": "",
                    "RW": true,
                    "Propagation": "rprivate"
                },
                {
                    "Type": "bind",
                    "Source": "/root/stats/mariadb/data",
                    "Destination": "/var/lib/mysql",
                    "Mode": "",
                    "RW": true,
                    "Propagation": "rprivate"
                }
            ]
        },
        {
            현재 실행중인 컨테이너 개수만큼 출력
        },
"Volumes": [
        {
            "CreatedAt": "2020-08-13T16:08:34+09:00",
            "Driver": "local",
            "Labels": null,
            "Mountpoint": "/var/lib/docker/volumes/67f8e209eac7a47669a3fd507f2927592817bfb5b0dc5711f80b2018b4a8b5b8/_data",
            "Name": "67f8e209eac7a47669a3fd507f2927592817bfb5b0dc5711f80b2018b4a8b5b8",
            "Options": null,
            "Scope": "local",
            "UsageData": {
                "RefCount": 0,
                "Size": 0
            }
        },
        {
            현재 마운트 되어있는 볼륨 수 만큼 출력
        },
"BuildCache": null,
"BuilderSize": 0
```

<br><br>

__func (*Client) Info__<br>
current data usage from the daemon 리턴

```go
go func() {
    defer w.Done()
    info, err := cli.Info(ctx)
    if err != nil {
        panic(err)
    }
    doc, _ := json.MarshalIndent(info, "", "    ")
    fmt.Println(string(doc))
}()
```

```bash
{
	"ID": "PWJX:UJNB:2KEG:3UYL:WNSK:XDFS:VP5R:BOOW:2D3A:3EZJ:TQFU:AJSZ",
	"Containers": 2,
	"ContainersRunning": 2,
	"ContainersPaused": 0,
	"ContainersStopped": 0,
	"Images": 201,
	"Driver": "overlay2",
	"DriverStatus": [
		[
			"Backing Filesystem",
			"\u003cunknown\u003e"
		],
		[
			"Supports d_type",
			"true"
		],
		[
			"Native Overlay Diff",
			"true"
		]
	],
	"Plugins": {
		"Volume": [
			"local"
		],
		"Network": [
			"bridge",
			"host",
			"ipvlan",
			"macvlan",
			"null",
			"overlay"
		],
		"Authorization": null,
		"Log": [
			"awslogs",
			"fluentd",
			"gcplogs",
			"gelf",
			"journald",
			"json-file",
			"local",
			"logentries",
			"splunk",
			"syslog"
		]
	},
	"MemoryLimit": true,
	"SwapLimit": true,
	"KernelMemory": true,
	"KernelMemoryTCP": true,
	"CpuCfsPeriod": true,
	"CpuCfsQuota": true,
	"CPUShares": true,
	"CPUSet": true,
	"PidsLimit": true,
	"IPv4Forwarding": true,
	"BridgeNfIptables": true,
	"BridgeNfIp6tables": true,
	"Debug": false,
	"NFd": 41,
	"OomKillDisable": true,
	"NGoroutines": 53,
	"SystemTime": "2021-01-26T15:40:33.2325057+09:00",
	"LoggingDriver": "json-file",
	"CgroupDriver": "systemd",
	"NEventsListener": 0,
	"KernelVersion": "3.10.0-1062.12.1.el7.x86_64",
	"OperatingSystem": "CentOS Linux 7 (Core)",
	"OSVersion": "",
	"OSType": "linux",
	"Architecture": "x86_64",
	"IndexServerAddress": "https://index.docker.io/v1/",
	"RegistryConfig": {
		"AllowNondistributableArtifactsCIDRs": [],
		"AllowNondistributableArtifactsHostnames": [],
		"InsecureRegistryCIDRs": [
			"127.0.0.0/8"
		],
		"IndexConfigs": {
			"192.168.102.142:5002": {
				"Name": "192.168.102.142:5002",
				"Mirrors": [],
				"Secure": false,
				"Official": false
			},
			"docker.io": {
				"Name": "docker.io",
				"Mirrors": [],
				"Secure": true,
				"Official": true
			}
		},
		"Mirrors": []
	},
	"NCPU": 16,
	"MemTotal": 67557842944,
	"GenericResources": null,
	"DockerRootDir": "/var/lib/docker",
	"HttpProxy": "",
	"HttpsProxy": "",
	"NoProxy": "",
	"Name": "k8s-dev",
	"Labels": [],
	"ExperimentalBuild": false,
	"ServerVersion": "19.03.8",
	"Runtimes": {
		"runc": {
			"path": "runc"
		}
	},
	"DefaultRuntime": "runc",
	"Swarm": {
		"NodeID": "",
		"NodeAddr": "",
		"LocalNodeState": "inactive",
		"ControlAvailable": false,
		"Error": "",
		"RemoteManagers": null
	},
	"LiveRestoreEnabled": false,
	"Isolation": "",
	"InitBinary": "docker-init",
	"ContainerdCommit": {
		"ID": "7ad184331fa3e55e52b890ea95e65ba581ae3429",
		"Expected": "7ad184331fa3e55e52b890ea95e65ba581ae3429"
	},
	"RuncCommit": {
		"ID": "dc9208a3303feef5b3839f4323d9beb36df0a9dd",
		"Expected": "dc9208a3303feef5b3839f4323d9beb36df0a9dd"
	},
	"InitCommit": {
		"ID": "fec3683",
		"Expected": "fec3683"
	},
	"SecurityOptions": [
		"name=seccomp,profile=default"
	],
	"Warnings": null
}
```

<br><br>

__func (*Client) VolumeInspect__<br>
docker volume inspect [volume name] 과 동일<br>
docker volume name은 'docker volume ls' 로 얻을 수 있다. 


```go
go func() {
    defer w.Done()
    volumeinfo, err := cli.VolumeInspect(ctx, "22a58bf06feeb387ff4fdf1bbfe2572ca7b75f3ab6f14af4c5977daf9e63b1e2")
    if err != nil {
        panic(err)
    }
    doc, _ := json.MarshalIndent(volumeinfo, "", "     ")
    fmt.Println(string(doc))
}()
```

```bash
{
	"CreatedAt": "2021-01-11T14:09:31+09:00",
	"Driver": "local",
	"Labels": null,
	"Mountpoint": "/var/lib/docker/volumes/22a58bf06feeb387ff4fdf1bbfe2572ca7b75f3ab6f14af4c5977daf9e63b1e2/_data",
	"Name": "22a58bf06feeb387ff4fdf1bbfe2572ca7b75f3ab6f14af4c5977daf9e63b1e2",
	"Options": null,
	"Scope": "local"
}
```


<br><br>

## Docker Swarm 에러

```
func (*Client) NodeList <br>
func (*Client) SecretList <br>
func (*Client) ServiceList <br>
func (*Client) TaskList <br>
```

<br>

```bash
panic: Error response from daemon: This node is not a swarm manager. Use "docker swarm init" or "docker swarm join" to connect this node to swarm and try again.
```

<br><br>

## CPU 사용량 계산법
https://forums.docker.com/t/how-to-calculate-the-cpu-usage-in-percent/27509



















