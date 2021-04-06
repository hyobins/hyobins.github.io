---
layout: post
published: On
title: TSDB | InfluxDB Query Example
category: monitoring
subtitle: InfluxDB - Query 응용 예시 정리
date: '2021-03-23'
---  

# Multiple Fields

[공식문서-Overview](https://docs.influxdata.com/influxdb/cloud/query-data/common-queries/multiple-fields-in-calculations/) <br>
[공식문서-Functions](https://docs.influxdata.com/influxdb/v2.0/reference/flux/stdlib/built-in/transformations/)

## filter() functions
```filter()``` 사용시 요청한 measurement의 특정 필드 값만 리턴받을 수 있다. <br>
이때 ```or``` 을 사용하여 여러 필드를 가져올 수 있다. 여러 필드를 호출하더라도 output은 하나의 field key-value 쌍을 출력한다. <br>
즉 아래와 같이 A or B field를 호출하면 2개의 field에 대한 2개의 테이블이 출력된다. 

```sh
|> filter(fn:(r) =>
		r._measurement == "${measurement 이름}" and
		r._field == "utime" or r._field == "stime")
```

output table

|_time | _field | _value | contName
|--|--|--|--|
|2021-03-23T21:48|utime|0.840993206| name0

---

|_time | _field | _value | contName
|--|--|--|--|
|2021-03-23T21:48|stime|0.000993206| name0

<br>

## pivot() functions
```pivot()``` 를 사용하면 row key를 기준으로(보통 time값) 가지면서 여러 필드의 값을 column으로 가지며 한 row에 나타낼 수 있다. <br>

```sh
|> filter(fn:(r) =>
		r._measurement == "${measurement 이름}" and
		r._field == "utime" or r._field == "stime")
|> pivot(rowKey:["_time"], columnKey: ["_field"], valueColumn: "_value")
```

output table

|_time | utime | stime | contName
|--|--|--|--|
|2021-03-23T21:48|0.840993206 | 0.000993206 |name0

<br>

columnKey에 여러 column 을 조합할 수 있다.

```sh
|> pivot(
	rowKey:["_time"], 
	columnKey: ["contName", "_field"], 
	valueColumn: "_value"
)
```

output table

|_time | name0_utime | name0_stime | contName
|--|--|--|--|
|2021-03-23T21:48| 0.840993206 | 0.000993206 | name0
|2021-03-23T23:48| 0.870993206 | 0.001093206 | name0


<br>

## map() functions
```map()``` 를 이용하여 새로운 column을 추가할 수 있다.

```sh
|> map(fn: (r) => ({ r with newColum: r.utime + r.stime}))
```

<br>

## Examples

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

output table

|_time | utime | stime | _value(계산값)
|--|--|--|--|
|2021-03-23T01:41:18 |0.60466028797 | 0.94050908804| 1.5451693760
|2021-03-23T05:42:48 |0.60466028797 | 0.94050908804| 1.5451693760
|2021-03-23T05:53:09 |0.60466028797 | 0.94050908804| 1.5451693760

<br>

Output

```sh
Result: _results
Table: keys: [_measurement, _start, _stop, contName, containerID, vendor]
   _measurement:string                     _start:time                      _stop:time         contName:string      containerID:string           vendor:string                  _value:float                   stime:float                   utime:float                      _time:time
----------------------  ------------------------------  ------------------------------  ----------------------  ----------------------  ----------------------  ----------------------------  ----------------------------  ----------------------------  ------------------------------
rand-buck  2021-03-23T00:14:59.557030871Z  2021-03-23T08:14:59.557030871Z                   name0                 contid0                 mobigen             1.545169376024632            0.9405090880450124            0.6046602879796196  2021-03-23T01:41:18.134184438Z
rand-buck  2021-03-23T00:14:59.557030871Z  2021-03-23T08:14:59.557030871Z                   name0                 contid0                 mobigen             1.545169376024632            0.9405090880450124            0.6046602879796196  2021-03-23T05:42:48.414859117Z
rand-buck  2021-03-23T00:14:59.557030871Z  2021-03-23T08:14:59.557030871Z                   name0                 contid0                 mobigen             1.545169376024632            0.9405090880450124            0.6046602879796196  2021-03-23T05:53:09.296947480Z
```