# #655 Refresh azure-pipelines.yml [Closed]

> Username: sdarwin  
> Created at: 2021-12-02 23:40:57 UTC  
> Updated at: 2021-12-05 13:14:47 UTC  
> Closed at: 2021-12-05 13:14:47 UTC  
> Url: https://github.com/boostorg/json/pull/655  

A newer copy of azure-pipelines.yml from boostorg/boost-ci. The interesting result from these tests is that the following combinations don't pass (and have been disabled).   That may have been expected.  
- clang 3.5  
- clang 3.6  
- clang 6, libc++, debug   
  
You could uncomment the jobs, to view more info.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-12-03 00:54:04 UTC  
> Url: https://github.com/boostorg/json/pull/655#issuecomment-985123073  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/655/pullrequest-condensed-a7144d0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/655/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/655/pullrequest.html)

---

## Comment 2

> Username: grisumbras  
> Created_at: 2021-12-05 13:14:47 UTC  
> Url: https://github.com/boostorg/json/pull/655#issuecomment-986228344  

Merged via #656

---
