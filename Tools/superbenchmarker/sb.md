 superbenchMarker简介及使用
 ---
- 简介

简称sb,Superbenchmarker是用于对HTTP API和网站进行性能测试的负载生成器命令行工具。受Apache Benchmark的启发，它打算成为steriods上的Apache Benchmark（ab.exe）。它会在测试结束时在终端窗口中显示最终结果，但也会在Web界面中不断报告。

SuperBenchmarker(sb.exe)可以在Windows或者Mac上运行(尚未在Linux上进行测试),他可以安装在.NET4.52+或者.NET Core2.0+。

- 安装

     * window用户

        可以直接在github的download目录内或者exe文件

        [sb.exe下载链接](https://github.com/aliostad/SuperBenchmarker/tree/master/download)

    *  mac用户
        > git clone https://github.com/aliostad/SuperBenchmarker <br>
          cd SuperBenchmarker <br>
          ./build.sh
- 参数详解
```
    -c, --concurrency            (Default: 1) Number of concurrent requests//并发线程数
      
    -n, --numberOfRequests       (Default: 100) Total number of requests//请求次数
      
    -N, --numberOfSeconds        Number of seconds to run the test. If specified, -n will be ignored.//测试时长，如果有此参数，-n则被忽略

    -u, --url                    Required. Target URL to call. Can include placeholders.//请求url

    -y, --delayInMillisecond     (Default: 0) Delay in millisecond//延迟毫秒数

    -m, --method                 (Default: GET) HTTP Method to use//指定请求的方法
    
    -h, --headers                Displays headers for request and response// 
```
- 举例说明
```
// 4个线程，测试30分钟
sb -u "http://example.com" -c 4 -N 1800

// 一个线程，请求100次
sb -u "http://example.com" -c 1 -n 100
```
- 结果说明
这边有简易的测试统计，可以看出每秒可处理多少的Request、最大的处理时间、最小的处理时间、平均的处理时间、以及压了这么多次的API，依比例分大概在哪个范围。
```
F:\tools>sb -u http://localhost:8801/ -c 40 -N 60
Starting at 2021/2/12 13:14:40
[Press C to stop the test]
10238   (RPS: 161.4)
---------------Finished!----------------
10239   (RPS: 161.4)                    Finished at 2021/2/12 13:15:43 (took 00:01:03.5228717)
10252   (RPS: 161.6)                    Status 200:    10252

RPS: 167.6 (requests/second)
Max: 302ms
Min: 39ms
Avg: 233.7ms

  50%   below 234ms
  60%   below 235ms
  70%   below 235ms
  80%   below 236ms
  90%   below 237ms
  95%   below 238ms
  98%   below 239ms
  99%   below 241ms
99.9%   below 257ms

```
同时还有网页来展现测试统计结果

- 参考链接
    * [SuperBenchmarker github官方网站](https://github.com/aliostad/SuperBenchmarker)