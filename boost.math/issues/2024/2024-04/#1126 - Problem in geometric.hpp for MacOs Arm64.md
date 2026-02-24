# #1126 - Problem in geometric.hpp for MacOs Arm64 [Closed]

> Username: kenarab  
> Created at: 2024-04-29 18:24:31 UTC  
> Updated at: 2024-05-22 07:51:22 UTC  
> Closed at: 2024-05-22 07:51:22 UTC  
> Url: https://github.com/boostorg/math/issues/1126  

This line should be corrected  
At /math/distributions/geometric.hpp at line 404  
```  
  return std::log(p); // success_fraction  
        
```  
Now the code is  
```  
  return log(p); // success_fraction  
        
```  
  
  
When building that library. Throws this error  
  
```  
In file included from /opt/homebrew/include/boost/math/distributions.hpp:26:  
/opt/homebrew/include/boost/math/distributions/geometric.hpp:404:16: error: unexpected namespace name 'log': expected expression  
        return log(p); // success_fraction  
               ^  
1 error generated.  
make[2]: *** [CMakeFiles/PonisIqCoreNLV.dir/Unity/unity_0_cxx.cxx.o] Error 1  
make[1]: *** [CMakeFiles/PonisIqCoreNLV.dir/all] Error 2  
make: *** [all] Error 2  
```

---

## Comment 1

> Username: kenarab  
> Created at: 2024-04-29 18:24:55 UTC  
> Updated at: 2024-04-29 18:25:43 UTC  
> Url: https://github.com/boostorg/math/issues/1126#issuecomment-2083380786  

I will try to correct by my self on the repo, but don't know if it is possible  
Editing this file in a fork  
https://github.com/boostorg/math/blob/develop/include/boost/math/distributions/geometric.hpp
