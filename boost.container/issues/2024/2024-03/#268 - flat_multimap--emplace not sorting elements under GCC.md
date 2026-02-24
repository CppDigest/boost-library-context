# #268 - flat_multimap::emplace not sorting elements under GCC [Closed]

> Username: OFFTKP  
> Created at: 2024-03-07 21:43:23 UTC  
> Updated at: 2024-03-07 23:35:03 UTC  
> Closed at: 2024-03-07 23:35:02 UTC  
> Url: https://github.com/boostorg/container/issues/268  

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

> Username: OFFTKP  
> Created at: 2024-03-07 23:35:02 UTC  
> Url: https://github.com/boostorg/container/issues/268#issuecomment-1984781867  

accidentally opened twice, closing
