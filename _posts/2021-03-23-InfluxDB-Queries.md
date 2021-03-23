---
layout: post
published: On
title: Prometheus | InfluxDB Query Example
category: monitoring
subtitle: InfluxDB - Query 응용 예시 정리
date: '2021-03-23'
---  

# Multiple Fields

[공식문서](https://docs.influxdata.com/influxdb/cloud/query-data/common-queries/multiple-fields-in-calculations/)

## Filter by Fields 
```filter()``` 사용시 요청한 필드의 값만 리턴받을 수 있다. <br>
이때 ```or``` 을 사용하여 여러 필드를 가져올 수 있다.

```sh
|> filter(fn:(r) =>
		r._measurement == "rand-buck" and
		r._field == "utime" or r._field == "stime")
```

## Pivot Fileds into columns
```pivot()``` 를 사용하면 시간을 row key로 가지면서 여러 필드의 값을 한 row에 나타낼 수 있다.

```sh
|> pivot(rowKey:["_time"], columnKey: ["_field"], valueColumn: "_value")
```

## 계산
```map()``` 를 이용하여 필드 값 별로 계산할 수 있다.

```sh
|> map(fn: (r) => ({ r with _value: r.utime + r.stime}))
```

<br>

## 전체 쿼리문

```sh
root@88dd1dd67800:/# influx query -
data = from(bucket: "rand-buck")
	|> range(start: -8h)
	|> filter(fn:(r) =>
		r._measurement == "rand-buck" and
		r._field == "utime" or r._field == "stime")
	|> pivot(rowKey:["_time"], columnKey: ["_field"], valueColumn: "_value")
	|> map(fn: (r) => ({ r with _value: r.utime + r.stime}))
	|> yield(name: "_results")
```

```sh
Result: _results
Table: keys: [_measurement, _start, _stop, contName, containerID, vendor]
   _measurement:string                     _start:time                      _stop:time         contName:string      containerID:string           vendor:string                  _value:float                   stime:float                   utime:float                      _time:time
----------------------  ------------------------------  ------------------------------  ----------------------  ----------------------  ----------------------  ----------------------------  ----------------------------  ----------------------------  ------------------------------
rand-buck  2021-03-23T00:14:59.557030871Z  2021-03-23T08:14:59.557030871Z                   name0                 contid0                 mobigen             
1.545169376024632            0.9405090880450124            0.6046602879796196  2021-03-23T01:41:18.134184438Z
rand-buck  2021-03-23T00:14:59.557030871Z  2021-03-23T08:14:59.557030871Z                   name0                 contid0                 mobigen             
1.545169376024632            0.9405090880450124            0.6046602879796196  2021-03-23T05:42:48.414859117Z
rand-buck  2021-03-23T00:14:59.557030871Z  2021-03-23T08:14:59.557030871Z                   name0                 contid0                 mobigen             
1.545169376024632            0.9405090880450124            0.6046602879796196  2021-03-23T05:53:09.296947480Z
```