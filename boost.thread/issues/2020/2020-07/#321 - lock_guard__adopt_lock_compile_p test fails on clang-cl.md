# #321 - lock_guard__adopt_lock_compile_p test fails on clang-cl [Closed]

> Username: Kojoley  
> Created at: 2020-07-03 22:53:42 UTC  
> Updated at: 2023-09-11 15:13:46 UTC  
> Closed at: 2023-09-11 15:13:46 UTC  
> Url: https://github.com/boostorg/thread/issues/321  

`b2 toolset=clang-win libs/thread/test//lock_guard__adopt_lock_compile_p`  
```  
libs\thread\test\sync\mutual_exclusion\locks\lock_guard\lock_guard_adopt_lock_compile_pass.cpp(21,35): error: calling function 'lock_guard' requires holding mutex 'm' exclusively [-Werror,-Wthread-safety-analysis]  
  boost::lock_guard<boost::mutex> lk(m, boost::adopt_lock);  
                                  ^  
```
