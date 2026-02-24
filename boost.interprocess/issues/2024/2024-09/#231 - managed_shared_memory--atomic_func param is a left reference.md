# #231 - managed_shared_memory::atomic_func  param is a left reference [Closed]

> Username: qq1174159858  
> Created at: 2024-09-26 06:29:47 UTC  
> Updated at: 2026-01-08 15:52:40 UTC  
> Closed at: 2026-01-08 15:52:40 UTC  
> Url: https://github.com/boostorg/interprocess/issues/231  

template <class Func>  
   void atomic_func(Func &f)  
   {   mp_header->atomic_func(f);}  
  
  
why not   
template <class Func>  
   void atomic_func(Func &&f)  
   {   mp_header->atomic_func(std::forward<Func>(func));}

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-07 08:57:41 UTC  
> Url: https://github.com/boostorg/interprocess/issues/231#issuecomment-3717900007  

Reviewing old bugs, many thaks for your report.. What's the advantage of taking the param as a universal reference?
