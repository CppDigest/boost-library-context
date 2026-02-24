# #875 - Set cxxstd=20 on benchmarks [Closed]

> Username: sdarwin  
> Created at: 2023-03-22 10:46:10 UTC  
> Updated at: 2023-04-18 15:43:09 UTC  
> Closed at: 2023-04-18 15:43:09 UTC  
> Url: https://github.com/boostorg/json/issues/875  

and upgrade benchmark servers to Ubuntu 22.04

---

## Comment 1

> Username: sdarwin  
> Created at: 2023-04-03 15:07:37 UTC  
> Url: https://github.com/boostorg/json/issues/875#issuecomment-1494502688  

Upgraded operating system, compiler version, and cxxstd.  
  
```  
$ gcc --version  
gcc (Ubuntu 11.3.0-1ubuntu1~22.04) 11.3.0  
  
$ clang --version  
Ubuntu clang version 14.0.0-1ubuntu1  
```  
  
See attached image.   
  
- 2023-03-11 through 2023-03-22 was the earlier configuration.   
- 2023-03-24 through 2023-03-27 after the upgrade the frequency lock was temporarily disabled. On these server I'm locking the frequency of the CPU to a low value to improve the stability and repeatability of results.  
- 2023-03-28 through 2023-03-31 after the upgrade, and the frequency lock is back in place. Results are 10% better than historical numbers.  
- 2023-04-02 Set cxxstd=20. another 1% better.  
  
Explore all the results at https://benchmark.cppalliance.org/ Some benchmarks improved after the system upgrade and others are slightly worse. It depends on the benchmark test.  
  
![benchmarks](https://user-images.githubusercontent.com/1257803/229547646-b114e9fc-15c1-432f-8ec6-1fb29c1141ed.png)

---

## Comment 2

> Username: sdarwin  
> Created at: 2023-04-18 15:43:09 UTC  
> Url: https://github.com/boostorg/json/issues/875#issuecomment-1513387698  

Benchmarks have been running correctly with the new cxxstd=20 flag. Closing issue.
