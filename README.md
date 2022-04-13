# Simple HTTP Server
The project implements a simple HTTP server using two asynchronous APIs on Linux: epoll and io_uring for benchmark.
Both servers use two worker threads for processing HTTP requests and autocannon was used for benchmarking as wrk is
not working on Raspberry Pi.  Since epoll only support socket and it can only poll readiness of a descriptor.  It was only
used for accept() and read().  By contrast, io_uring(liburing) only support completeness of a descriptor and support file I/O.
It is used for both socket and file I/O. 

## Platform Information
| Hardware Model  |           CPU           |        RAM         |                OS                |
|:---------------:|:-----------------------:|:------------------:|:--------------------------------:|
| Raspberry Pi 4B | 4 x Cortex-A72 @ 1.5GHz | 4GB 3200MHz LPDDR4 | Linux alarmpi 5.15.32-3-rpi-ARCH |

## Performance (epoll)
```
[root@alarmpi ~]# autocannon 'http://127.0.0.1:8080/script.js'
Running 10s test @ http://127.0.0.1:8080/script.js
10 connections

┌─────────┬────────┬─────────┬─────────┬─────────┬────────────┬────────────┬─────────┐
│ Stat    │ 2.5%   │ 50%     │ 97.5%   │ 99%     │ Avg        │ Stdev      │ Max     │
├─────────┼────────┼─────────┼─────────┼─────────┼────────────┼────────────┼─────────┤
│ Latency │ 236 ms │ 2308 ms │ 4188 ms │ 4257 ms │ 2312.94 ms │ 1135.22 ms │ 4317 ms │
└─────────┴────────┴─────────┴─────────┴─────────┴────────────┴────────────┴─────────┘
┌───────────┬────────┬────────┬─────────┬─────────┬─────────┬────────┬────────┐
│ Stat      │ 1%     │ 2.5%   │ 50%     │ 97.5%   │ Avg     │ Stdev  │ Min    │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤
│ Req/Sec   │ 820    │ 820    │ 1571    │ 1718    │ 1459.4  │ 309.22 │ 820    │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤
│ Bytes/Sec │ 869 kB │ 869 kB │ 1.67 MB │ 1.82 MB │ 1.55 MB │ 328 kB │ 869 kB │
└───────────┴────────┴────────┴─────────┴─────────┴─────────┴────────┴────────┘

Req/Bytes counts sampled once per second.
# of samples: 10

29k requests in 10.05s, 15.5 MB read
```

## Performance (io_uring)
```
[root@alarmpi ~]# autocannon 'http://127.0.0.1:8080/script.js'
Running 10s test @ http://127.0.0.1:8080/script.js
10 connections

┌─────────┬────────┬─────────┬─────────┬─────────┬────────────┬────────────┬─────────┐
│ Stat    │ 2.5%   │ 50%     │ 97.5%   │ 99%     │ Avg        │ Stdev      │ Max     │
├─────────┼────────┼─────────┼─────────┼─────────┼────────────┼────────────┼─────────┤
│ Latency │ 246 ms │ 2280 ms │ 4150 ms │ 4213 ms │ 2289.16 ms │ 1108.44 ms │ 4298 ms │
└─────────┴────────┴─────────┴─────────┴─────────┴────────────┴────────────┴─────────┘
┌───────────┬────────┬────────┬─────────┬─────────┬─────────┬────────┬────────┐
│ Stat      │ 1%     │ 2.5%   │ 50%     │ 97.5%   │ Avg     │ Stdev  │ Min    │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤
│ Req/Sec   │ 770    │ 770    │ 1624    │ 1726    │ 1441.4  │ 329.15 │ 770    │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤
│ Bytes/Sec │ 817 kB │ 817 kB │ 1.72 MB │ 1.83 MB │ 1.53 MB │ 349 kB │ 816 kB │
└───────────┴────────┴────────┴─────────┴─────────┴─────────┴────────┴────────┘

Req/Bytes counts sampled once per second.
# of samples: 10

29k requests in 10.06s, 15.3 MB read
```

## Known Issues
The liburing seems not be able to fully sent large files on ARM platforms.  If you try to access http://127.0.0.1:8080/jquery.js,
only half of the file will be sent.

## Sponsor
It takes a lot of time to do researches.  If you think the project helped you, could you buy me a cup of coffee? 😉  
You can use any of the following methods to donate:  

| [![PayPal](/img/paypal.svg)](https://www.paypal.com/paypalme/tianchentang)<br/>Click [here](https://www.paypal.com/paypalme/tianchentang) to donate | ![Wechat Pay](/img/wechat.jpg)<br/>Wechat Pay | ![Alipay](/img/alipay.jpg) Alipay |
|-----------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------|-----------------------------------|
