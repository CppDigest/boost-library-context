# #2947 - Latency performance from HTTP awaitable server examples [Closed]

> Username: shuwens  
> Created at: 2024-11-01 20:56:22 UTC  
> Updated at: 2025-04-15 13:14:01 UTC  
> Closed at: 2025-04-15 13:14:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2947  

Hey,  
  
Thank you all for all the work on boost/beast, it is a great library to use. I am working on a project which needs an embedded HTTP server, and I switched from civetweb to beast. One thing I am wondering is the actual achievable latency of beast. My project will be doing a large amount of HTTP requests so throughput and latency matter to me.   
  
I run the http awaitable server with 1.86 against wrk. The benchmarking result is ranging from 250us to 400us. Is this the lowest latency I can achieve with boost beast, or am i missing anything here?  
  
  
Regards,  
  
An example is as follows.   
```sh  
16 threads and 80 connections  
  Thread Stats   Avg      Stdev     Max   +/- Stdev  
    Latency   258.48us  335.09us  17.77ms   98.98%  
    Req/Sec    20.56k   800.05    26.78k    80.45%  
  3304893 requests in 10.10s, 267.90MB read  
Requests/sec: 327223.28  
Transfer/sec:     26.53MB  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-11-25 19:14:39 UTC  
> Updated at: 2024-11-25 19:22:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2947#issuecomment-2498835848  

Hi, sorry for the late response. I somehow missed the notification for this issue.  
  
There are a few strategies that can help lower latency:  
  
- Preload files into a container (e.g., `std::string`) during startup. Use [http::string_body](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__string_body.html) instead of `file_body` to avoid blocking system calls for file reads.  
  
- If you're testing with 80 connections and 16 threads, requests will be queued (there is no computing resource to respond). This means you're measuring queue latency rather than pure processing latency.  
  
- Prevent the OS scheduler from moving threads between cores. Here's an example for Linux:    
```cpp  
cpu_set_t cpuset;  
CPU_ZERO(&cpuset);  
CPU_SET(0, &cpuset); // Specify core  
int rc = pthread_setaffinity_np(pthread_self(), sizeof(cpu_set_t), &cpuset);  
if (rc != 0)  
      std::cerr << "Error calling pthread_setaffinity_np: " << rc << "\n";  
```  
  
- Prevent threads from sleeping by replacing `ioc.run()` with an infinite loop that repeatedly calls `ioc.poll()`:  
  
```cpp  
for(;;)  
    ioc.poll();  
```  
  
---  
  
With **8 threads and 8 connections**, the following results were observed:  
```  
wrk -t8 -c8 -d5s http://127.0.0.1:8080/index.html  
Running 5s test @ http://127.0.0.1:8080/index.html  
  8 threads and 8 connections  
  Thread Stats   Avg      Stdev     Max   +/- Stdev  
    Latency    18.82us    7.24us   1.29ms   97.14%  
    Req/Sec    51.79k     1.61k   57.22k    69.36%  
  2102673 requests in 5.10s, 232.61MB read  
Requests/sec: 412298.77  
Transfer/sec:     45.61MB  
```  
Processor: `13th Gen Intel(R) Core(TM) i7-13700`  
Server: `http-server-awaitable 0.0.0.0 8080 . 8`
