# #700 Try to fix test build [Closed]

> Username: danieljames  
> Created at: 2017-07-29 22:24:28 UTC  
> Updated at: 2017-07-30 00:16:55 UTC  
> Closed at: 2017-07-30 00:16:55 UTC  
> Url: https://github.com/boostorg/beast/pull/700  

When I run b2 in the status directory, I'm getting this error:  
  
    error: Name clash for  
    '<p../bin.v2/libs/coroutine/build/gcc-6.3.0/debug/link-static/threading-multi>libboost_coroutine.a'  
    error:  
    error: Tried to build the target twice, with property sets having  
    error: these incompatible properties:  
    error:  
    error:     -  <define>BOOST_COROUTINES_NO_DEPRECATION_WARNING=1  
    error:     -  none  
    error:  
    error: Please make sure to have consistent requirements for these  
    error: properties everywhere in your project, especially for install  
    error: targets.  
  
This seems to be caused by different libraries depending on  
boost_coroutine and only one of them using this feature. This fixes  
that.  
  
Btw. I think your jamfile should be in a 'build' subdirectory.

---
