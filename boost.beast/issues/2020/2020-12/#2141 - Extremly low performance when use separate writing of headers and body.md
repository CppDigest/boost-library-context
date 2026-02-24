# #2141 - Extremly low performance when use separate writing of headers and body [Closed]

> Username: andrejlevkovitch  
> Created at: 2020-12-17 18:42:18 UTC  
> Updated at: 2021-01-04 11:27:44 UTC  
> Closed at: 2021-01-04 11:27:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2141  

beast version 189  
g++-9  
  
  
I have multithread async echo http server, that print to response user requests. So when I try at first write headers and after it write body of the response, then I have extremely low performance. Here is a some test results by wrk:  
  
1. Write headers and body by only call (`async_write`) with one thread  
  
```  
Running 1m test @ http://localhost:8011/echo  
  8 threads and 16 connections  
  Thread Stats   Avg      Stdev     Max   +/- Stdev  
    Latency   239.31us   83.76us   8.35ms   99.67%  
    Req/Sec     8.31k   167.69     8.87k    67.41%  
  3972822 requests in 1.00m, 353.30MB read  
Requests/sec:  66103.76  
Transfer/sec:      5.88MB  
```  
  
2. Write headers and body by only call (`async_write`) with four threads  
  
```  
Running 1m test @ http://localhost:8011/echo  
  8 threads and 16 connections  
  Thread Stats   Avg      Stdev     Max   +/- Stdev  
    Latency   132.11us  526.43us  26.85ms   97.53%  
    Req/Sec    26.74k     3.41k   38.27k    70.17%  
  12776705 requests in 1.00m, 1.11GB read  
Requests/sec: 212794.69  
Transfer/sec:     18.92MB  
```  
  
3. Write headers and body separately (by sequent calling `async_write_header` and `async_write`) with one thread  
  
```  
Running 1m test @ http://localhost:8011/echo  
  8 threads and 16 connections  
  Thread Stats   Avg      Stdev     Max   +/- Stdev  
    Latency    44.55ms    1.86ms  50.47ms   84.05%  
    Req/Sec    44.84      6.32    60.00     92.54%  
  21522 requests in 1.00m, 1.92MB read  
Requests/sec:    358.65  
Transfer/sec:     32.68KB  
```  
  
  
4. Write headers and body separately (by sequent calling `async_write_header` and `async_write`) with four threads  
  
```  
Running 1m test @ http://localhost:8011/echo  
  8 threads and 16 connections  
  Thread Stats   Avg      Stdev     Max   +/- Stdev  
    Latency    44.79ms    2.05ms  51.88ms   78.22%  
    Req/Sec    44.60      6.17    60.00     93.38%  
  21412 requests in 1.00m, 1.91MB read  
Requests/sec:    356.28  
Transfer/sec:     32.47KB  
```  
  
Summary: in case of using separate writing of headers and body server can handle only 21 thousand of request per minute (capacity of threads doesn't matter). But if we write headers and body at once then server can handle around 3-4 millions requests per thread at same time!  
  
Why performance is so low?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-12-17 18:46:18 UTC  
> Updated at: 2020-12-17 18:46:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2141#issuecomment-747626917  

What happens with the most recent version of Beast? Are you using SSL?

---

## Comment 2

> Username: andrejlevkovitch  
> Created at: 2020-12-17 18:57:01 UTC  
> Updated at: 2020-12-17 18:57:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2141#issuecomment-747632960  

@vinniefalco thank for so fast response. No, I don't use ssl. And I didn't use boost newer then 1.69

---

## Comment 3

> Username: roddypratt  
> Created at: 2020-12-30 10:31:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2141#issuecomment-752408993  

This sounds like a classic TCP Nagle problem. The second packet isn't sent until the ACK is received for the first one. Setting TCP_NODELAY on the sender is one fix, but it's often better for the application layer to avoid multiple writes and combine them if possible.

---

## Comment 4

> Username: andrejlevkovitch  
> Created at: 2021-01-04 11:27:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2141#issuecomment-753921407  

@roddypratt yes! You are absolutely right! I completely forget about delaying. Thank
