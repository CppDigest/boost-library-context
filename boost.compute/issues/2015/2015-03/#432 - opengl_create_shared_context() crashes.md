# #432 - opengl_create_shared_context() crashes [Closed]

> Username: salmanulhaq  
> Created at: 2015-03-03 09:12:08 UTC  
> Updated at: 2017-04-10 07:49:21 UTC  
> Closed at: 2017-04-10 07:49:21 UTC  
> Url: https://github.com/boostorg/compute/issues/432  

I've CUDA 6.5 toolkit and latest drivers installed on my Ubuntu 14.04. Normal context creation works just fine. Creating a shared context fails. I've freeglut3 and glew installed as well as Qt5.4  
##

---

## Comment 1

> Username: salmanulhaq  
> Created at: 2015-03-03 12:15:46 UTC  
> Url: https://github.com/boostorg/compute/issues/432#issuecomment-76934855  

I've pinned it down to this call:  
  
``` c++  
if(!gpu.supports_extension(cl_gl_sharing_extension)){  
    continue;  
}   
```  
  
This gives me segmentation fault so I thought `cl_gl_sharing_extension` (const char*) maybe the cause. I commented this call out but then same "Segmentation fault" error with the context creation call below it:  
  
``` c++  
return context(gpu, properties);  
```  
  
`properties` array looks ok.

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-03-03 16:28:12 UTC  
> Url: https://github.com/boostorg/compute/issues/432#issuecomment-76979434  

Thanks for the report. Could you compile with debugging symbols enabled and provide a back-trace from the crash?

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-04-10 07:49:21 UTC  
> Url: https://github.com/boostorg/compute/issues/432#issuecomment-292874753  

I think https://github.com/boostorg/compute/pull/677 fixed that problem. Please reopen if the bug still occurs (remember to use `develop` branch).
