# #266 - macos build error [Closed]

> Username: egorpugin  
> Created at: 2022-08-12 18:36:00 UTC  
> Updated at: 2022-09-02 12:29:22 UTC  
> Closed at: 2022-09-02 12:29:22 UTC  
> Url: https://github.com/boostorg/process/issues/266  

g++-12, apple m1, boost 1.80.0  
  
```  
In file included from /Users/egor/.sw/storage/pkg/9d/a6/8d0d/src/sdir/src/command/src/command.cpp:19:  
In file included from /Users/egor/.sw/storage/pkg/57/b0/5812/src/sdir/include/boost/process.hpp:24:  
In file included from /Users/egor/.sw/storage/pkg/57/b0/5812/src/sdir/include/boost/process/async_system.hpp:22:  
In file included from /Users/egor/.sw/storage/pkg/57/b0/5812/src/sdir/include/boost/process/child.hpp:22:  
In file included from /Users/egor/.sw/storage/pkg/57/b0/5812/src/sdir/include/boost/process/detail/execute_impl.hpp:24:  
/Users/egor/.sw/storage/pkg/57/b0/5812/src/sdir/include/boost/process/detail/posix/executor.hpp:156:36: error: non-constant-expression cannot be narrowed from type 'unsigned long' to 'int' in initializer list [-Wc++11-narrowing]  
        int data[2] = {ec.value(), len + 1};  
                                   ^~~~~~~  
/Users/egor/.sw/storage/pkg/57/b0/5812/src/sdir/include/boost/process/detail/posix/executor.hpp:156:36: note: insert an explicit cast to silence this issue  
        int data[2] = {ec.value(), len + 1};  
                                   ^~~~~~~  
                                   static_cast<int>( )  
```  
  
https://github.com/egorpugin/tgbot/runs/7811967093?check_suite_focus=true

---

## Comment 1

> Username: phillvancejr  
> Created at: 2022-08-26 20:29:28 UTC  
> Url: https://github.com/boostorg/process/issues/266#issuecomment-1228919449  

same with clang++ 13.1.6, Monterey 12.5.1, intel
