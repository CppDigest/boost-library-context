# #36 - void (__cdecl *)(const char *,const void *,size_t,void *)”　and　“void (__stdcall *)(const char *,const void *,size_t,void *)” [Closed]

> Username: azuredsky  
> Created at: 2014-01-02 13:36:58 UTC  
> Updated at: 2015-02-08 19:46:18 UTC  
> Closed at: 2014-03-09 03:57:52 UTC  
> Url: https://github.com/boostorg/compute/issues/36  

In windows ,there's an error with void (__cdecl *) in the header:context.hpp   
　static void __stdcall default_error_handler(const char *errinfo,  
                                      const void *private_info,  
                                      size_t cb,  
                                      void *user_data)  
  
I added  __stdcall , it works well .

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-01-02 15:24:52 UTC  
> Url: https://github.com/boostorg/compute/issues/36#issuecomment-31459059  

Thanks for the report.  
  
This should have been fixed by 7db9ad715f. Have you tried with the latest version?  
  
-kyle

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-03-09 03:57:52 UTC  
> Url: https://github.com/boostorg/compute/issues/36#issuecomment-37118407  

Assumed fixed. Please re-open if you still are having issues.
