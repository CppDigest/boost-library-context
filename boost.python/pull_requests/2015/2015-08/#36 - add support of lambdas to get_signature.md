# #36 add support of lambdas to get_signature [Open]

> Username: nevion  
> Created at: 2015-08-16 12:31:09 UTC  
> Updated at: 2020-11-14 02:30:48 UTC  
> Url: https://github.com/boostorg/python/pull/36  

see #34   
This does not entirely add support for lambda's, but does make it work for get_signature  
  
Presently there is this error (in detail::invoke) when passing a lambda :  
https://gist.github.com/nevion/bb90bb9a9b0f7071dbf2  
  
I'm not sure how to proceed with this and will wait for some insights.  
  
It also depends on a type trait that is better off in the type_traits or function_types libraries, and while it looks good it is hard to know if it works in every situation though should always suffice for lambdas - so there may be some justification for keeping it local, at least in the near term.  
  
Also: in ripping out all the macros for pre c++11 support, I probably broke something for older C++03 or lower.  But given how hard they are to understand and the pain of not using C++11 with boost.... I'm not sure I really would want to go down the road of trying to support those compilers.  On the other hand, I ended up, to my knowledge, not using any C++11 with a potential exception of forwarding with boost::forward.  All the sub-libraries used already deal with the gory details.  I also did it in such a way that it was drop in switchable, maybe unnecessarily.

---
