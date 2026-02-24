# #242 - Is the performance of boost context slow on kernel 3.10 like Centos7 [Closed]

> Username: zhangh43  
> Created at: 2023-10-13 06:57:43 UTC  
> Updated at: 2024-03-10 19:31:30 UTC  
> Closed at: 2024-03-10 19:31:30 UTC  
> Url: https://github.com/boostorg/context/issues/242  

I run the same boost context program on centos7(kernel 3) and ubuntu2004(kernel 5). The centos7 is much more slower the Ubuntu2004. Is it expected?

---

## Comment 1

> Username: olk  
> Created at: 2024-03-10 19:29:55 UTC  
> Url: https://github.com/boostorg/context/issues/242#issuecomment-1987339048  

no idea - it should not depend on the kernel because the kernel is not involved when switch between fibers
