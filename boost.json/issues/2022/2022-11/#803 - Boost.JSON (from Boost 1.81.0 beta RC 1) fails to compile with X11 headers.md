# #803 - Boost.JSON (from Boost 1.81.0 beta RC 1) fails to compile with X11 headers [Closed]

> Username: mclow  
> Created at: 2022-11-11 18:42:40 UTC  
> Updated at: 2022-11-11 20:52:27 UTC  
> Closed at: 2022-11-11 20:52:27 UTC  
> Url: https://github.com/boostorg/json/issues/803  

Captured from the boost mailing list (11/11/22, in a message about the Boost 1.81.0 beta 1 release candidate:  
  
I am getting compile errors when using the beta.  It seems that Boost.Json uses the identifier Opposite as a template argument, but X.h (from X11) contains the following line:  
  
 #define Opposite                4  
  
This is a regression from Boost version 1.80.0.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-11-11 18:56:50 UTC  
> Updated at: 2022-11-11 18:57:03 UTC  
> Url: https://github.com/boostorg/json/issues/803#issuecomment-1312073171  

Yeah, I've seen it on ML. Will fix soon.
