# #4 Bugfix: forced_return<T>() did lead to compile-time errors for move-only... [Closed]

> Username: ralphtandetzky  
> Created at: 2014-04-11 08:08:51 UTC  
> Updated at: 2014-06-23 16:12:25 UTC  
> Closed at: 2014-06-23 16:12:25 UTC  
> Url: https://github.com/boostorg/variant/pull/4  

... types.  
  
When implementing a visitor that returns an std::unique_ptr<T> the code compiled in Visual Studio 2010 and 2012, but not with GCC 4.8.2 since the copy constructor is required in the line   
  
```  
return *static_cast< basic_type* >(dummy);  
```  
  
This line is not compiled under Visual Studio since it is checked whether the macro BOOST_MSVC is defined. It should be ok to throw here, since the code is not called at run-time anyways.   
  
Throwing should work in most circumstances, but I'm not sure what to do in the case where RTTI is disabled and hence exceptions are not allowed. Possibly a recursive call to forced_return<T>() is an alternative here. I don't fully comprehend what forced_return is needed for. If someone's reading this, please check if   
  
```  
return forced_return<T>();  
```  
  
would not be a better line to put here.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2014-05-13 13:37:22 UTC  
> Url: https://github.com/boostorg/variant/pull/4#issuecomment-42955152  

You are right: such fix will break compilation of Boost.Variant with exceptions off. Tests are also required to make sure that such error won't appear again.  
  
I can take care of this feature, but unfortunately that won't happen soon.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-06-23 16:12:25 UTC  
> Url: https://github.com/boostorg/variant/pull/4#issuecomment-46866156  

Fixed it in 0367512  
Thank you for reporting the issue!

---
