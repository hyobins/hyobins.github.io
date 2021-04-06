---
layout: post
published: On
title: TSDB | InfluxDB Performance Test
category: monitoring
subtitle: InfluxDB - 성능 테스트 정리
date: '2021-04-06'
---  

config

```yml
tagLength : 10
numTags: 5
numField: 7
numGoroutine: 20
writePerSecond: 1000
# total goruotine * write per second
batchSize: 5000
```

## 결과 요약
측정 시간 : 5분 <br>

|분류 | CPU% 평균| CPU% max | CPU% min | %MEM 
|--|--|--|--|--|
|influxd(deamon)| 239.34|575.0|100.0|8.7
|influx-sim(실행파일)|


## 참고
실행 명령어
```sh
[root@node01 influxdb-sim]# top -p {pid} -b -n100 | grep ${pid} > ${파일이름}.txt
```

<details><summary>결과값</summanry>

```sh
[root@node01 influxdb-sim]# top -p 6577 -b -n100 | grep 6577 > influxd-test2.txt
#결과값
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 6577 centos    20   0 5270796   2.7g   1.5g S 100.0  8.7 315:41.80 influxd
 6577 centos    20   0 5270796   2.7g   1.5g S 174.7  8.7 315:47.04 influxd
 6577 centos    20   0 5274404   2.7g   1.5g S 278.7  8.7 315:55.40 influxd
 6577 centos    20   0 5274404   2.7g   1.5g S 191.3  8.7 316:01.14 influxd
 6577 centos    20   0 5274404   2.7g   1.5g S 186.0  8.7 316:06.74 influxd
 6577 centos    20   0 5278032   2.7g   1.5g S 259.3  8.7 316:14.52 influxd
 6577 centos    20   0 5278032   2.7g   1.5g S 215.7  8.7 316:20.99 influxd
 6577 centos    20   0 5281684   2.7g   1.5g S 280.4  8.7 316:29.43 influxd
 6577 centos    20   0 5349292   2.7g   1.5g S 255.3  8.7 316:37.09 influxd
 6577 centos    20   0 5352900   2.7g   1.5g S 240.9  8.7 316:44.34 influxd
 6577 centos    20   0 5352900   2.7g   1.5g S 196.3  8.7 316:50.23 influxd
 6577 centos    20   0 5352900   2.7g   1.5g S 183.9  8.7 316:50.80 influxd
 6577 centos    20   0 5352900   2.7g   1.5g S 575.0  8.7 316:52.18 influxd
 6577 centos    20   0 5352900   2.7g   1.5g S 105.9  8.7 316:52.54 influxd
 6577 centos    20   0 5352900   2.7g   1.5g S 108.1  8.7 316:52.94 influxd
 6577 centos    20   0 5352900   2.7g   1.5g S 330.8  8.7 316:53.80 influxd
 6577 centos    20   0 5356532   2.7g   1.5g S 333.9  8.7 317:03.85 influxd
 6577 centos    20   0 5356532   2.7g   1.5g S 189.0  8.7 317:09.52 influxd
 6577 centos    20   0 5356532   2.7g   1.5g S 189.0  8.7 317:15.19 influxd
 6577 centos    20   0 5360608   2.7g   1.5g S 303.0  8.7 317:24.28 influxd
 6577 centos    20   0 5360608   2.7g   1.5g S 189.0  8.7 317:29.95 influxd
 6577 centos    20   0 5364188   2.7g   1.5g S 278.7  8.7 317:38.34 influxd
 6577 centos    20   0 5364188   2.7g   1.5g S 202.3  8.7 317:44.41 influxd
 6577 centos    20   0 5364188   2.7g   1.5g S 183.0  8.7 317:49.90 influxd
 6577 centos    20   0 5367936   2.7g   1.5g S 259.5  8.7 317:57.71 influxd
 6577 centos    20   0 5367936   2.7g   1.5g S 190.7  8.7 318:03.43 influxd
 6577 centos    20   0 5371688   2.7g   1.5g S 280.3  8.7 318:11.84 influxd
 6577 centos    20   0 5371688   2.7g   1.5g S 189.7  8.7 318:17.53 influxd
 6577 centos    20   0 5375332   2.7g   1.5g S 315.7  8.7 318:27.00 influxd
 6577 centos    20   0 5375332   2.7g   1.5g S 159.1  8.7 318:31.79 influxd
 6577 centos    20   0 5375332   2.7g   1.5g S 210.7  8.7 318:38.11 influxd
 6577 centos    20   0 5379424   2.8g   1.5g S 320.7  8.8 318:47.73 influxd
 6577 centos    20   0 5379376   2.7g   1.5g S 243.2  8.7 318:55.05 influxd
 6577 centos    20   0 5382924   2.7g   1.5g S 281.0  8.7 319:03.48 influxd
 6577 centos    20   0 5382924   2.7g   1.5g S 176.3  8.7 319:08.77 influxd
 6577 centos    20   0 5382924   2.7g   1.5g S 212.3  8.7 319:15.14 influxd
 6577 centos    20   0 5386556   2.7g   1.5g S 272.7  8.7 319:23.32 influxd
 6577 centos    20   0 5386556   2.7g   1.5g S 190.7  8.7 319:29.04 influxd
 6577 centos    20   0 5390152   2.7g   1.5g S 251.2  8.7 319:36.60 influxd
 6577 centos    20   0 5390152   2.7g   1.5g S 218.0  8.7 319:43.14 influxd
 6577 centos    20   0 5393788   2.7g   1.5g S 270.1  8.7 319:51.27 influxd
 6577 centos    20   0 5393788   2.7g   1.5g S 191.7  8.7 319:57.02 influxd
 6577 centos    20   0 5393788   2.7g   1.5g S 182.4  8.7 320:02.51 influxd
 6577 centos    20   0 5397448   2.7g   1.5g S 258.7  8.7 320:10.27 influxd
 6577 centos    20   0 5397448   2.7g   1.5g S 200.0  8.7 320:16.27 influxd
 6577 centos    20   0 5401060   2.7g   1.5g S 331.0  8.7 320:26.20 influxd
 6577 centos    20   0 5401060   2.7g   1.5g S 201.0  8.7 320:32.23 influxd
 6577 centos    20   0 5401060   2.7g   1.5g S 210.3  8.7 320:38.56 influxd
 6577 centos    20   0 5405136   2.7g   1.5g S 306.0  8.7 320:47.77 influxd
 6577 centos    20   0 5405136   2.7g   1.5g S 189.7  8.7 320:53.46 influxd
 6577 centos    20   0 5409156   2.7g   1.5g S 272.0  8.7 321:01.62 influxd
 6577 centos    20   0 5409100   2.8g   1.5g S 294.0  8.8 321:10.47 influxd
 6577 centos    20   0 5409100   2.8g   1.5g S 315.0  8.8 321:19.92 influxd
 6577 centos    20   0 5412708   2.8g   1.5g S 342.0  8.9 321:30.18 influxd
 6577 centos    20   0 5412708   2.8g   1.5g S 322.3  9.0 321:39.85 influxd
 6577 centos    20   0 5416696   2.8g   1.6g S 370.1  9.0 321:50.99 influxd
 6577 centos    20   0 5416696   2.8g   1.6g S 285.7  9.1 321:59.56 influxd
 6577 centos    20   0 5416668   2.7g   1.5g S 280.7  8.7 322:08.01 influxd
 6577 centos    20   0 5420220   2.7g   1.5g S 249.7  8.7 322:15.50 influxd
 6577 centos    20   0 5420220   2.7g   1.5g S 194.7  8.7 322:21.34 influxd
 6577 centos    20   0 5423828   2.7g   1.5g S 277.7  8.7 322:29.67 influxd
 6577 centos    20   0 5423828   2.7g   1.5g S 201.0  8.7 322:35.72 influxd
 6577 centos    20   0 5427480   2.7g   1.5g S 274.3  8.7 322:43.95 influxd
 6577 centos    20   0 5427480   2.7g   1.5g S 222.0  8.7 322:50.61 influxd
 6577 centos    20   0 5427480   2.7g   1.5g S 198.3  8.7 322:56.58 influxd
 6577 centos    20   0 5431060   2.7g   1.5g S 260.0  8.7 323:04.38 influxd
 6577 centos    20   0 5431060   2.7g   1.5g S 211.3  8.7 323:10.72 influxd
 6577 centos    20   0 5434700   2.7g   1.5g S 264.8  8.7 323:18.69 influxd
 6577 centos    20   0 5434700   2.7g   1.5g S 209.7  8.7 323:24.98 influxd
 6577 centos    20   0 5438340   2.7g   1.5g S 264.0  8.7 323:32.90 influxd
 6577 centos    20   0 5438292   2.7g   1.5g S 252.0  8.7 323:40.46 influxd
 6577 centos    20   0 5438292   2.7g   1.5g S 194.0  8.7 323:46.30 influxd
 6577 centos    20   0 5441928   2.7g   1.5g S 257.7  8.7 323:54.03 influxd
 6577 centos    20   0 5441928   2.7g   1.5g S 206.7  8.7 324:00.23 influxd
 6577 centos    20   0 5445564   2.7g   1.5g S 309.7  8.7 324:09.52 influxd
 6577 centos    20   0 5445564   2.7g   1.5g S 208.0  8.7 324:15.78 influxd
 6577 centos    20   0 5449148   2.7g   1.5g S 294.3  8.7 324:24.61 influxd
 6577 centos    20   0 5449148   2.7g   1.5g S 196.7  8.7 324:30.53 influxd
 6577 centos    20   0 5449148   2.7g   1.5g S 189.7  8.7 324:36.22 influxd
 6577 centos    20   0 5452740   2.7g   1.5g S 213.0  8.7 324:42.61 influxd
 6577 centos    20   0 5452740   2.7g   1.5g S 203.0  8.7 324:48.70 influxd
 6577 centos    20   0 5456452   2.7g   1.5g S 259.5  8.7 324:56.51 influxd
 6577 centos    20   0 5456452   2.7g   1.5g S 199.7  8.7 325:02.50 influxd
 6577 centos    20   0 5460128   2.7g   1.5g S 312.0  8.7 325:11.86 influxd
 6577 centos    20   0 5460128   2.7g   1.5g S 220.3  8.7 325:18.49 influxd
 6577 centos    20   0 5460128   2.7g   1.5g S 169.7  8.7 325:23.58 influxd
 6577 centos    20   0 5464236   2.7g   1.5g S 288.7  8.7 325:32.24 influxd
 6577 centos    20   0 5464236   2.7g   1.5g S 211.0  8.7 325:38.57 influxd
 6577 centos    20   0 5467816   2.7g   1.5g S 256.0  8.7 325:46.25 influxd
 6577 centos    20   0 5467772   2.7g   1.5g S 266.1  8.7 325:54.26 influxd
 6577 centos    20   0 5467772   2.7g   1.5g S 216.7  8.7 326:00.76 influxd
 6577 centos    20   0 5471364   2.7g   1.5g S 265.0  8.7 326:08.71 influxd
 6577 centos    20   0 5471364   2.7g   1.5g S 195.3  8.7 326:14.59 influxd
 6577 centos    20   0 5474932   2.7g   1.5g S 264.0  8.7 326:22.51 influxd
 6577 centos    20   0 5474932   2.7g   1.5g S 172.7  8.7 326:27.69 influxd
 6577 centos    20   0 5478592   2.7g   1.5g S 297.0  8.7 326:36.63 influxd
 6577 centos    20   0 5478592   2.7g   1.5g S 203.7  8.7 326:42.74 influxd
 6577 centos    20   0 5478592   2.7g   1.5g S 252.7  8.7 326:50.32 influxd
 6577 centos    20   0 5482236   2.7g   1.5g S 200.7  8.7 326:56.34 influxd
 6577 centos    20   0 5482236   2.7g   1.5g S 187.0  8.7 327:01.97 influxd
```

</details>


<br><br>

config

```yml
tagLength : 10
numTags: 5
numField: 7
numGoroutine: 20
writePerSecond: 2000
# total goruotine * write per second
batchSize: 5000
```

5분간 측정

## 결과 요약
측정 시간 : 5분 <br>

|분류 | CPU% 평균| CPU% max | CPU% min | %MEM 
|--|--|--|--|--|
|influxd(deamon)| 240.47 |773.3|161.3|10.3
|influx-sim(실행파일)|

```sh
[root@node01 influxdb-sim]# top -p 6577 -b -n100 | grep 6577 > influxd-test3.txt

 6577 centos    20   0 5771548   3.2g   1.9g S 773.3 10.3 377:27.56 influxd
 6577 centos    20   0 5775156   3.2g   1.9g S 205.7 10.3 377:33.73 influxd
 6577 centos    20   0 5775156   3.2g   1.9g S 211.0 10.3 377:40.06 influxd
 6577 centos    20   0 5778800   3.2g   1.9g S 229.6 10.3 377:46.97 influxd
 6577 centos    20   0 5778800   3.2g   1.9g S 201.3 10.3 377:53.01 influxd
 6577 centos    20   0 5782424   3.2g   1.9g S 324.0 10.3 378:02.73 influxd
 6577 centos    20   0 5782424   3.2g   1.9g S 162.1 10.3 378:07.61 influxd
 6577 centos    20   0 5782424   3.2g   1.9g S 191.3 10.3 378:13.35 influxd
 6577 centos    20   0 5786500   3.2g   1.9g S 278.3 10.3 378:21.70 influxd
 6577 centos    20   0 5786500   3.2g   1.9g S 189.3 10.3 378:27.38 influxd
 6577 centos    20   0 5790164   3.2g   1.9g S 282.0 10.3 378:35.84 influxd
 6577 centos    20   0 5790164   3.2g   1.9g S 171.8 10.3 378:41.01 influxd
 6577 centos    20   0 5790164   3.2g   1.9g S 275.7 10.3 378:49.28 influxd
 6577 centos    20   0 5793752   3.2g   1.9g S 215.7 10.3 378:55.75 influxd
 6577 centos    20   0 5793752   3.2g   1.9g S 199.0 10.3 379:01.74 influxd
 6577 centos    20   0 5797404   3.3g   1.9g S 317.7 10.4 379:11.27 influxd
 6577 centos    20   0 5797352   3.2g   1.9g S 222.3 10.3 379:17.94 influxd
 6577 centos    20   0 5800956   3.2g   1.9g S 355.7 10.3 379:28.61 influxd
 6577 centos    20   0 5800956   3.2g   1.9g S 175.0 10.3 379:33.86 influxd
 6577 centos    20   0 5800956   3.2g   1.9g S 193.4 10.3 379:39.68 influxd
 6577 centos    20   0 5804936   3.2g   1.9g S 273.3 10.3 379:47.88 influxd
 6577 centos    20   0 5804936   3.2g   1.9g S 190.3 10.3 379:53.59 influxd
 6577 centos    20   0 5808632   3.2g   1.9g S 247.8 10.3 380:01.05 influxd
 6577 centos    20   0 5808632   3.2g   1.9g S 196.0 10.3 380:06.93 influxd
 6577 centos    20   0 5808632   3.2g   1.9g S 261.0 10.3 380:14.76 influxd
 6577 centos    20   0 5812288   3.2g   1.9g S 202.7 10.3 380:20.84 influxd
 6577 centos    20   0 5812288   3.2g   1.9g S 176.3 10.3 380:26.13 influxd
 6577 centos    20   0 5815956   3.2g   1.9g S 303.3 10.3 380:35.26 influxd
 6577 centos    20   0 5815956   3.2g   1.9g S 200.0 10.3 380:41.26 influxd
 6577 centos    20   0 5819492   3.2g   1.9g S 259.7 10.3 380:49.05 influxd
 6577 centos    20   0 5819492   3.2g   1.9g S 166.1 10.3 380:54.05 influxd
 6577 centos    20   0 5819492   3.2g   1.9g S 248.0 10.3 381:01.49 influxd
 6577 centos    20   0 5823180   3.2g   1.9g S 209.3 10.3 381:07.77 influxd
 6577 centos    20   0 5823180   3.2g   1.9g S 195.7 10.3 381:13.64 influxd
 6577 centos    20   0 5826892   3.3g   1.9g S 306.6 10.4 381:22.87 influxd
 6577 centos    20   0 5826840   3.3g   1.9g S 310.7 10.4 381:32.19 influxd
 6577 centos    20   0 5826840   3.3g   2.0g S 351.0 10.5 381:42.72 influxd
 6577 centos    20   0 5830868   3.3g   2.0g S 319.9 10.5 381:52.35 influxd
 6577 centos    20   0 5830868   3.3g   2.0g S 309.0 10.6 382:01.62 influxd
 6577 centos    20   0 5834804   3.4g   2.0g S 365.1 10.7 382:12.61 influxd
 6577 centos    20   0 5834784   3.2g   1.9g S 236.3 10.3 382:19.70 influxd
 6577 centos    20   0 5834784   3.2g   1.9g S 266.3 10.3 382:27.69 influxd
 6577 centos    20   0 5838400   3.2g   1.9g S 199.7 10.3 382:33.68 influxd
 6577 centos    20   0 5838400   3.2g   1.9g S 171.3 10.3 382:38.82 influxd
 6577 centos    20   0 5842032   3.2g   1.9g S 266.8 10.3 382:46.85 influxd
 6577 centos    20   0 5842032   3.2g   1.9g S 189.7 10.3 382:52.54 influxd
 6577 centos    20   0 5845724   3.2g   1.9g S 289.3 10.3 383:01.22 influxd
 6577 centos    20   0 5845724   3.2g   1.9g S 174.3 10.3 383:06.45 influxd
 6577 centos    20   0 5845724   3.2g   1.9g S 217.6 10.3 383:13.00 influxd
 6577 centos    20   0 5849808   3.2g   1.9g S 292.7 10.3 383:21.78 influxd
 6577 centos    20   0 5849808   3.2g   1.9g S 217.3 10.3 383:28.30 influxd
 6577 centos    20   0 5853440   3.2g   1.9g S 243.2 10.3 383:35.62 influxd
 6577 centos    20   0 5853440   3.2g   1.9g S 189.7 10.3 383:41.31 influxd
 6577 centos    20   0 5853440   3.2g   1.9g S 275.0 10.3 383:49.56 influxd
 6577 centos    20   0 5857048   3.3g   1.9g S 270.7 10.4 383:57.68 influxd
 6577 centos    20   0 5857048   3.3g   1.9g S 190.4 10.4 384:03.41 influxd
 6577 centos    20   0 5860644   3.3g   1.9g S 263.3 10.4 384:11.31 influxd
 6577 centos    20   0 5860644   3.3g   1.9g S 234.3 10.4 384:18.34 influxd
 6577 centos    20   0 5864216   3.3g   1.9g S 229.3 10.4 384:25.22 influxd
 6577 centos    20   0 5864216   3.3g   1.9g S 197.3 10.4 384:31.16 influxd
 6577 centos    20   0 5864216   3.3g   1.9g S 277.0 10.4 384:39.47 influxd
 6577 centos    20   0 5867932   3.3g   1.9g S 184.3 10.4 384:45.00 influxd
 6577 centos    20   0 5867932   3.3g   1.9g S 163.0 10.4 384:49.89 influxd
 6577 centos    20   0 5871540   3.3g   1.9g S 287.0 10.4 384:58.50 influxd
 6577 centos    20   0 5871540   3.3g   1.9g S 198.0 10.4 385:04.46 influxd
 6577 centos    20   0 5875092   3.3g   1.9g S 237.7 10.4 385:11.59 influxd
 6577 centos    20   0 5875092   3.3g   1.9g S 189.0 10.4 385:17.26 influxd
 6577 centos    20   0 5875092   3.3g   1.9g S 283.1 10.4 385:25.78 influxd
 6577 centos    20   0 5878724   3.3g   1.9g S 220.7 10.4 385:32.40 influxd
 6577 centos    20   0 5878724   3.3g   1.9g S 215.0 10.4 385:38.85 influxd
 6577 centos    20   0 5882448   3.3g   1.9g S 245.3 10.4 385:46.21 influxd
 6577 centos    20   0 5882448   3.3g   1.9g S 211.3 10.4 385:52.57 influxd
 6577 centos    20   0 5886052   3.3g   1.9g S 296.3 10.5 386:01.46 influxd
 6577 centos    20   0 5885992   3.3g   1.9g S 232.3 10.4 386:08.43 influxd
 6577 centos    20   0 5885992   3.3g   1.9g S 196.3 10.4 386:14.32 influxd
 6577 centos    20   0 5890052   3.3g   1.9g S 280.7 10.4 386:22.77 influxd
 6577 centos    20   0 5890052   3.3g   1.9g S 200.7 10.4 386:28.79 influxd
 6577 centos    20   0 5893644   3.3g   1.9g S 242.3 10.4 386:36.06 influxd
 6577 centos    20   0 5893644   3.3g   1.9g S 188.7 10.4 386:41.74 influxd
 6577 centos    20   0 5897344   3.3g   1.9g S 265.7 10.4 386:49.71 influxd
 6577 centos    20   0 5897344   3.3g   1.9g S 186.0 10.4 386:55.29 influxd
 6577 centos    20   0 5897344   3.3g   1.9g S 161.3 10.4 387:00.13 influxd
 6577 centos    20   0 5901000   3.3g   1.9g S 312.0 10.4 387:09.49 influxd
 6577 centos    20   0 5901000   3.3g   1.9g S 197.3 10.4 387:15.43 influxd
 6577 centos    20   0 5904540   3.3g   1.9g S 269.3 10.4 387:23.51 influxd
 6577 centos    20   0 5904540   3.3g   1.9g S 192.4 10.4 387:29.30 influxd
 6577 centos    20   0 5904540   3.3g   1.9g S 266.0 10.4 387:37.28 influxd
 6577 centos    20   0 5908208   3.3g   1.9g S 210.7 10.4 387:43.60 influxd
 6577 centos    20   0 5908208   3.3g   1.9g S 186.4 10.4 387:49.21 influxd
 6577 centos    20   0 5911840   3.3g   1.9g S 265.7 10.4 387:57.18 influxd
 6577 centos    20   0 5911840   3.3g   1.9g S 197.3 10.4 388:03.10 influxd
 6577 centos    20   0 5915488   3.3g   1.9g S 277.7 10.5 388:11.43 influxd
 6577 centos    20   0 5915444   3.3g   1.9g S 222.6 10.4 388:18.13 influxd
 6577 centos    20   0 5919076   3.3g   1.9g S 266.7 10.4 388:26.13 influxd
 6577 centos    20   0 5919076   3.3g   1.9g S 184.7 10.4 388:31.67 influxd
 6577 centos    20   0 5919076   3.3g   1.9g S 204.0 10.4 388:37.79 influxd
 6577 centos    20   0 5922776   3.3g   1.9g S 250.5 10.4 388:45.33 influxd
 6577 centos    20   0 5922776   3.3g   1.9g S 203.7 10.4 388:51.44 influxd
 6577 centos    20   0 5926384   3.3g   1.9g S 310.6 10.4 389:00.79 influxd
 6577 centos    20   0 5926384   3.3g   1.9g S 188.0 10.4 389:06.43 influxd
 ```

<br><br>

config

```yml
tagLength : 10
numTags: 5
numField: 7
numGoroutine: 30
writePerSecond: 2000
# total goruotine * write per second
batchSize: 5000
```

5분간 측정

```sh
[root@node01 influxdb-sim]# top -p 6577 -b -n100 | grep 6577 > influxd-test3.txt
 6577 centos    20   0 5978416   3.4g   2.1g S 106.7 10.9 395:01.39 influxd
 6577 centos    20   0 5981996   3.4g   2.1g S 334.7 10.9 395:11.43 influxd
 6577 centos    20   0 5981996   3.4g   2.1g S 225.3 10.9 395:18.19 influxd
 6577 centos    20   0 5986384   3.4g   2.1g S 342.7 10.9 395:28.47 influxd
 6577 centos    20   0 5986384   3.4g   2.1g S 226.9 10.9 395:35.30 influxd
 6577 centos    20   0 5990700   3.4g   2.1g S 353.0 10.9 395:45.89 influxd
 6577 centos    20   0 5990700   3.4g   2.1g S 215.3 10.9 395:52.35 influxd
 6577 centos    20   0 5994960   3.4g   2.1g S 332.9 10.9 396:02.37 influxd
 6577 centos    20   0 5994960   3.4g   2.1g S 216.7 10.9 396:08.87 influxd
 6577 centos    20   0 5999300   3.4g   2.1g S 302.3 10.9 396:17.94 influxd
 6577 centos    20   0 5999300   3.4g   2.1g S 215.3 10.9 396:24.40 influxd
 6577 centos    20   0 6003468   3.4g   2.1g S 319.3 10.9 396:33.98 influxd
 6577 centos    20   0 6003468   3.4g   2.1g S 226.9 10.9 396:40.81 influxd
 6577 centos    20   0 6007980   3.5g   2.1g S 425.7 11.0 396:53.58 influxd
 6577 centos    20   0 6007880   3.4g   2.1g S 272.0 10.9 397:01.74 influxd
 6577 centos    20   0 6011864   3.4g   2.1g S 348.8 10.9 397:12.24 influxd
 6577 centos    20   0 6015488   3.4g   2.1g S 377.0 10.9 397:23.55 influxd
 6577 centos    20   0 6015488   3.4g   2.1g S 207.6 10.9 397:29.80 influxd
 6577 centos    20   0 6019920   3.4g   2.1g S 378.3 10.9 397:41.15 influxd
 6577 centos    20   0 6019920   3.4g   2.1g S 208.7 11.0 397:47.41 influxd
 6577 centos    20   0 6091076   3.5g   2.1g S 315.0 11.0 397:56.86 influxd
 6577 centos    20   0 6091076   3.5g   2.1g S 244.2 11.0 398:04.21 influxd
 6577 centos    20   0 6094952   3.5g   2.1g S 366.3 11.0 398:15.20 influxd
 6577 centos    20   0 6094952   3.5g   2.1g S 226.3 11.0 398:21.99 influxd
 6577 centos    20   0 6098752   3.5g   2.1g S 336.9 11.0 398:32.13 influxd
 6577 centos    20   0 6102308   3.5g   2.1g S 336.0 11.0 398:42.21 influxd
 6577 centos    20   0 6102308   3.5g   2.1g S 218.3 11.0 398:48.76 influxd
 6577 centos    20   0 6106788   3.5g   2.1g S 307.0 11.0 398:57.97 influxd
 6577 centos    20   0 6106532   3.5g   2.1g S 297.0 11.0 399:06.88 influxd
 6577 centos    20   0 6110776   3.5g   2.1g S 344.9 11.0 399:17.26 influxd
 6577 centos    20   0 6110776   3.5g   2.1g S 221.7 11.0 399:23.91 influxd
 6577 centos    20   0 6114324   3.5g   2.1g S 330.7 11.0 399:33.83 influxd
 6577 centos    20   0 6114324   3.5g   2.1g S 233.3 11.0 399:40.83 influxd
 6577 centos    20   0 6118604   3.5g   2.1g S 326.6 11.0 399:50.66 influxd
 6577 centos    20   0 6118604   3.5g   2.1g S 235.7 11.0 399:57.73 influxd
 6577 centos    20   0 6122824   3.5g   2.1g S 327.7 11.0 400:07.56 influxd
 6577 centos    20   0 6122824   3.5g   2.1g S 211.6 11.1 400:13.93 influxd
 6577 centos    20   0 6126488   3.5g   2.1g S 315.0 11.1 400:23.38 influxd
 6577 centos    20   0 6130164   3.5g   2.1g S 319.7 11.1 400:32.97 influxd
 6577 centos    20   0 6130164   3.5g   2.1g S 215.0 11.1 400:39.44 influxd
 6577 centos    20   0 6134476   3.5g   2.1g S 321.0 11.1 400:49.07 influxd
 6577 centos    20   0 6134476   3.5g   2.1g S 211.7 11.1 400:55.42 influxd
 6577 centos    20   0 6138772   3.5g   2.1g S 329.7 11.1 401:05.31 influxd
 6577 centos    20   0 6138724   3.5g   2.1g S 300.0 11.1 401:14.34 influxd
 6577 centos    20   0 6142912   3.5g   2.1g S 447.3 11.2 401:27.76 influxd
 6577 centos    20   0 6142912   3.5g   2.1g S 298.7 11.3 401:36.72 influxd
 6577 centos    20   0 6142912   3.6g   2.1g S 429.9 11.3 401:49.66 influxd
 6577 centos    20   0 6147012   3.6g   2.1g S 320.0 11.4 401:59.26 influxd
 6577 centos    20   0 6151080   3.6g   2.2g S 427.2 11.5 402:12.12 influxd
 6577 centos    20   0 6151080   3.6g   2.2g S 318.7 11.5 402:21.68 influxd
 6577 centos    20   0 6155320   3.6g   2.2g S 387.0 11.5 402:33.29 influxd
 6577 centos    20   0 6155292   3.5g   2.1g S 226.6 11.1 402:40.11 influxd
 6577 centos    20   0 6159428   3.5g   2.1g S 352.7 11.1 402:50.69 influxd
 6577 centos    20   0 6159428   3.5g   2.1g S 218.3 11.1 402:57.24 influxd
 6577 centos    20   0 6163136   3.5g   2.1g S 334.3 11.1 403:07.27 influxd
 6577 centos    20   0 6163136   3.5g   2.1g S 250.0 11.1 403:14.82 influxd
 6577 centos    20   0 6167372   3.5g   2.1g S 311.0 11.1 403:24.15 influxd
 6577 centos    20   0 6167372   3.5g   2.1g S 237.2 11.1 403:31.29 influxd
 6577 centos    20   0 6171636   3.5g   2.1g S 394.0 11.2 403:43.11 influxd
 6577 centos    20   0 6171540   3.5g   2.1g S 278.0 11.1 403:51.45 influxd
 6577 centos    20   0 6175088   3.5g   2.1g S 302.3 11.1 404:00.55 influxd
 6577 centos    20   0 6175088   3.5g   2.1g S 241.0 11.1 404:07.78 influxd
 6577 centos    20   0 6179400   3.5g   2.1g S 323.0 11.1 404:17.47 influxd
 6577 centos    20   0 6182920   3.5g   2.1g S 303.7 11.1 404:26.58 influxd
 6577 centos    20   0 6182920   3.5g   2.1g S 219.9 11.1 404:33.20 influxd
 6577 centos    20   0 6186616   3.5g   2.1g S 355.3 11.1 404:43.86 influxd
 6577 centos    20   0 6186616   3.5g   2.1g S 233.2 11.1 404:50.88 influxd
 6577 centos    20   0 6190376   3.5g   2.1g S 338.7 11.1 405:01.04 influxd
 6577 centos    20   0 6190376   3.5g   2.1g S 215.0 11.1 405:07.51 influxd
 6577 centos    20   0 6194608   3.5g   2.1g S 350.3 11.1 405:18.02 influxd
 6577 centos    20   0 6194608   3.5g   2.1g S 216.6 11.1 405:24.54 influxd
 6577 centos    20   0 6198688   3.5g   2.1g S 333.2 11.1 405:34.57 influxd
 6577 centos    20   0 6202468   3.5g   2.1g S 343.7 11.1 405:44.88 influxd
 6577 centos    20   0 6202528   3.5g   2.1g S 260.1 11.1 405:52.71 influxd
 6577 centos    20   0 6206316   3.5g   2.1g S 311.3 11.1 406:02.05 influxd
 6577 centos    20   0 6206316   3.5g   2.1g S 227.7 11.1 406:08.88 influxd
 6577 centos    20   0 6210604   3.5g   2.1g S 359.7 11.1 406:19.67 influxd
 6577 centos    20   0 6210604   3.5g   2.1g S 229.9 11.1 406:26.59 influxd
 6577 centos    20   0 6214980   3.5g   2.1g S 333.0 11.1 406:36.58 influxd
 6577 centos    20   0 6214980   3.5g   2.1g S 245.3 11.1 406:43.94 influxd
 6577 centos    20   0 6219252   3.5g   2.1g S 338.9 11.1 406:54.14 influxd
 6577 centos    20   0 6219252   3.5g   2.1g S 224.0 11.1 407:00.86 influxd
 6577 centos    20   0 6223512   3.5g   2.1g S 330.6 11.1 407:10.81 influxd
 6577 centos    20   0 6223512   3.5g   2.1g S 228.7 11.1 407:17.67 influxd
 6577 centos    20   0 6227068   3.5g   2.1g S 285.4 11.1 407:26.26 influxd
 6577 centos    20   0 6230688   3.5g   2.1g S 313.3 11.1 407:35.66 influxd
 6577 centos    20   0 6230688   3.5g   2.1g S 258.5 11.1 407:43.44 influxd
 6577 centos    20   0 6234272   3.5g   2.1g S 347.3 11.2 407:53.86 influxd
 6577 centos    20   0 6234224   3.5g   2.1g S 266.0 11.1 408:01.84 influxd
 6577 centos    20   0 6237792   3.5g   2.1g S 315.3 11.1 408:11.33 influxd
 6577 centos    20   0 6237792   3.5g   2.1g S 213.3 11.1 408:17.73 influxd
 6577 centos    20   0 6242184   3.5g   2.1g S 313.3 11.1 408:27.13 influxd
 6577 centos    20   0 6242184   3.5g   2.1g S 220.0 11.1 408:33.73 influxd
 6577 centos    20   0 6246480   3.5g   2.1g S 307.0 11.1 408:42.97 influxd
 6577 centos    20   0 6246480   3.5g   2.1g S 242.7 11.1 408:50.25 influxd
 6577 centos    20   0 6250876   3.5g   2.1g S 326.9 11.1 409:00.09 influxd
 6577 centos    20   0 6250876   3.5g   2.1g S 230.0 11.1 409:06.99 influxd
 6577 centos    20   0 6255120   3.5g   2.1g S 348.0 11.1 409:17.43 influxd
 6577 centos    20   0 6255120   3.5g   2.1g S 217.6 11.1 409:23.98 influxd
 6577 centos    20   0 6259424   3.5g   2.1g S 335.0 11.1 409:34.03 influxd
 ```