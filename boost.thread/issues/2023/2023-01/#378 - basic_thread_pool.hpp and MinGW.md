# #378 - basic_thread_pool.hpp and MinGW [Closed]

> Username: dyfer  
> Created at: 2023-01-22 18:33:30 UTC  
> Updated at: 2024-09-14 07:41:50 UTC  
> Closed at: 2024-09-14 07:41:50 UTC  
> Url: https://github.com/boostorg/thread/issues/378  

Hello,  
  
In SuperCollider we use boost and we use [this patch](https://github.com/supercollider/supercollider/pull/2787/commits/572c2c84ea8df4af55eab0536b72ca7fd91b3d93) for [basic_thread_pool.hpp](https://github.com/boostorg/thread/blob/develop/include/boost/thread/executors/basic_thread_pool.hpp#L149) to make it compile on MinGW. https://github.com/supercollider/supercollider/pull/2473 has more discussion on this issue.   
  
This patch seems to be still needed with Boost 1.80.0 (see [pending PR](https://github.com/supercollider/supercollider/pull/5924/commits/a5882bfe026ca746fe4516f36a85c57f741d0757)) so I'd like to report it here.  
  
Maybe adding this patch would fix https://github.com/boostorg/thread/issues/291, but I'm not sure.  
  
I hope you find this useful. Unfortunately, I don't have much expertise in this myself, just trying to raise awareness.

---

## Comment 1

> Username: dyfer  
> Created at: 2024-09-14 07:41:50 UTC  
> Url: https://github.com/boostorg/thread/issues/378#issuecomment-2350896819  

I think this is not an issue in the newer version of mingw/gcc.
