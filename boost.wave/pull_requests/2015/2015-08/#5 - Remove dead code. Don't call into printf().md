# #5 Remove dead code. Don't call into printf(). [Merged]

> Username: EdSchouten  
> Created at: 2015-08-27 14:40:02 UTC  
> Updated at: 2015-08-27 16:37:26 UTC  
> Merged at: 2015-08-27 16:37:26 UTC  
> Closed at: 2015-08-27 16:37:26 UTC  
> Url: https://github.com/boostorg/wave/pull/5  

There already seems to be an assertion in place that s->error_proc  
cannot be NULL. There are also various other parts of code that call  
into this function without checking for NULL first.  
  
The intent of this change is that it makes this code build with  
Nuxi CloudABI[1]. CloudABI is a sandboxed runtime computing environment.  
As it is mainly oriented towards running (networked) services, it does  
not understand the concept of stdin and stdout.  
  
[1] Nuxi CloudABI: https://github.com/NuxiNL/cloudlibc

---
