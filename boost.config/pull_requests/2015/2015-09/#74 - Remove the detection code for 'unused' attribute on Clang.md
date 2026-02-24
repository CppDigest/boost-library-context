# #74 Remove the detection code for 'unused' attribute on Clang [Merged]

> Username: morinmorin  
> Created at: 2015-09-03 09:40:46 UTC  
> Updated at: 2015-09-10 16:48:24 UTC  
> Merged at: 2015-09-03 09:54:05 UTC  
> Closed at: 2015-09-03 09:54:05 UTC  
> Url: https://github.com/boostorg/config/pull/74  

Clang has supported the 'unused' attribute since the first release.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-09-03 09:53:55 UTC  
> Url: https://github.com/boostorg/config/pull/74#issuecomment-137395416  

Thanks, that's much simpler!

---

## Comment 2

> Username: morinmorin  
> Created_at: 2015-09-10 15:26:14 UTC  
> Url: https://github.com/boostorg/config/pull/74#issuecomment-139279987  

This PR turns on `__attribute__((unused))` on all versions of Clang. But Clang 1.0 and 1.1 support the 'unused' attribute only for **variables and functions**; they do not accept the 'unused' attribute for **types** and emit warnings if we put the attribute.   
  
IMHO, Clang 1.0 and 1.1 are old enough to drop the support, but if you like, I'll of course revert the detection code to the original one:   
  
```  
#if defined(__GNUC__) && (__GNUC__ >= 4)  
```  
  
Sorry about the breakage.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-09-10 16:06:09 UTC  
> Url: https://github.com/boostorg/config/pull/74#issuecomment-139294280  

On 10/09/2015 16:26, morinmorin wrote:  
  
> This PR turns on |**attribute**((unused))| on all versions of Clang.   
> But Clang 1.0 and 1.1 support the 'unused' attribute only for   
> _variables and functions_; they do not accept the 'unused' attribute   
> for _types_ and emit warnings if we put the attribute.  
>   
> IMHO, Clang 1.0 and 1.1 are old enough to drop the support,  
  
+1, I seriously doubt anyone is using clang v1, and I suspect if they   
are, they would have other issues anyway?  
  
Suggest we leave it as it is for now, John.

---

## Comment 4

> Username: morinmorin  
> Created_at: 2015-09-10 16:48:24 UTC  
> Url: https://github.com/boostorg/config/pull/74#issuecomment-139307204  

> +1, I seriously doubt anyone is using clang v1, and I suspect if they are, they would have other issues anyway?  
  
Totally agreed.  
  
> Suggest we leave it as it is for now, John.  
  
OK, thanks!  
  
Regards,  
Michel

---
