# #17 Disable explicit operator bool on Oracle C++ [Merged]

> Username: jzmaddock  
> Created at: 2015-08-18 17:15:00 UTC  
> Updated at: 2015-08-18 17:54:50 UTC  
> Merged at: 2015-08-18 17:54:50 UTC  
> Closed at: 2015-08-18 17:54:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/17  

Although Oracle supports this syntax, advanced usage such as:  
  
if(my_shared_ptr && x)  
  
Fail.  Attempts to wokaround by adding additional operator overloads have so far failed.

---
