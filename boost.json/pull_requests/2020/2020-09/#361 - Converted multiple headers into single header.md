# #361 Converted multiple headers into single header  [Closed]

> Username: siddharth25pandey  
> Created at: 2020-09-18 16:54:54 UTC  
> Updated at: 2020-09-18 18:37:13 UTC  
> Closed at: 2020-09-18 18:37:13 UTC  
> Url: https://github.com/boostorg/json/pull/361  

As in bench directory,in bench.cpp file. There are unnecessary multiple header file which are already present in the **bits/stdc++.h**  
That can reduce the space as well code execution time.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-09-18 17:02:33 UTC  
> Url: https://github.com/boostorg/json/pull/361#issuecomment-694979560  

There are some problems with this pull request:  
  
1. Pull requests should be made against the **develop** branch, not the **master** branch  
  
2. `<bits/stdc++.h>` is implementation-specific and not a standard include file  
  
3. Your commit contains unnecessary whitespace changes which make reviewing more difficult

---

## Comment 2

> Username: siddharth25pandey  
> Created_at: 2020-09-18 17:11:50 UTC  
> Url: https://github.com/boostorg/json/pull/361#issuecomment-694983859  

@vinniefalco  <bits/stdc++.h> is implementation-specific but it already have headers like algorithm,vector,cstdio,etc.  
That will reduce the time cost as well as memory consumption.Please have a insider look  
Okay i will make PR against develop branch.  
Thanks!

---

## Comment 3

> Username: siddharth25pandey  
> Created_at: 2020-09-18 17:31:39 UTC  
> Url: https://github.com/boostorg/json/pull/361#issuecomment-694993665  

@vinniefalco Please give confirmation regarding this. So, that I will create a new PR against develop branch.  
Thanks!

---

## Comment 4

> Username: Olipro  
> Created_at: 2020-09-18 17:31:46 UTC  
> Url: https://github.com/boostorg/json/pull/361#issuecomment-694993719  

>   
>   
> @vinniefalco <bits/stdc++.h> is implementation-specific but it already have headers like algorithm,vector,cstdio,etc.  
> That will reduce the time cost as well as memory consumption.Please have a insider look  
> Okay i will make PR against develop branch.  
> Thanks!  
  
It will also make it entirely unusable on platforms which do not utilise the GCC standard C++ library. This is a non-starter for that reason, and for the fact it is also not conformant to standard, portable C++.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-09-18 18:01:15 UTC  
> Url: https://github.com/boostorg/json/pull/361#issuecomment-695007538  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/361/pullrequest-condensed-c868e14.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/361/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/361/pullrequest.html)

---
