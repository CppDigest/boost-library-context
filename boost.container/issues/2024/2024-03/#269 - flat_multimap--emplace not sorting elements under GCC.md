# #269 - flat_multimap::emplace not sorting elements under GCC [Closed]

> Username: OFFTKP  
> Created at: 2024-03-07 21:27:41 UTC  
> Updated at: 2024-05-23 21:55:12 UTC  
> Closed at: 2024-05-23 21:55:12 UTC  
> Url: https://github.com/boostorg/container/issues/269  

Hello!  
  
I have attached a code snippet.   
[main.txt](https://github.com/boostorg/boost/files/14530265/main.txt)  
  
  
If you build it like this:  
```  
g++ -O3 main.cpp  
```  
The output is not sorted, next timestamp should be 6000.  
```  
Next timestamp: 10000  
Time: 6000, TaskType: 0  
Time: 10000, TaskType: 1  
```  
Next timestamp should be 6000.  
  
If you compile with O2 using g++, or you compile using clang++ however, the issue is fixed:  
```  
clang++ -O3 main.cpp  
```  
  
```  
Next timestamp: 6000  
Time: 6000, TaskType: 0  
Time: 10000, TaskType: 1  
```

---

## Comment 1

> Username: wheremyfoodat  
> Created at: 2024-04-13 12:46:38 UTC  
> Url: https://github.com/boostorg/container/issues/269#issuecomment-2053637142  

I was also affected by this problem in https://github.com/wheremyfoodat/Panda3DS

---

## Comment 2

> Username: Lastique  
> Created at: 2024-04-20 11:21:17 UTC  
> Updated at: 2024-04-20 11:22:45 UTC  
> Url: https://github.com/boostorg/container/issues/269#issuecomment-2067643686  

This works as expected for me with Boost 1.84 but not 1.85. gcc 11.4, `-O2`, Kubuntu 22.04.

---

## Comment 3

> Username: Lastique  
> Created at: 2024-04-20 11:22:28 UTC  
> Url: https://github.com/boostorg/container/issues/269#issuecomment-2067643954  

Ah, sorry, I missed that you were using `-O3`. With it, it also fails with Boost 1.84.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2024-04-28 21:33:47 UTC  
> Url: https://github.com/boostorg/container/issues/269#issuecomment-2081663476  

Please check the following commit, it should fix the issue:  
  
https://github.com/boostorg/container/commit/20ad12f20e661978e90dc7f36d8ab8ac05e5a5a9

---

## Comment 5

> Username: Lastique  
> Created at: 2024-04-29 12:34:55 UTC  
> Url: https://github.com/boostorg/container/issues/269#issuecomment-2082616468  

With that commit applied to 1.85, this test produces the expected result for me:  
  
```  
Next timestamp: 6000  
Time: 6000, TaskType: 0  
Time: 10000, TaskType: 1  
```  
  
gcc 11.4, `-O3`, Kubuntu 22.04.

---

## Comment 6

> Username: igaztanaga  
> Created at: 2024-05-23 21:55:12 UTC  
> Url: https://github.com/boostorg/container/issues/269#issuecomment-2128080970  

Closing this as fixed after the commit. Thanks for the report.
