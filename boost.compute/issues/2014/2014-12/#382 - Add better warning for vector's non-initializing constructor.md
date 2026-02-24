# #382 - Add better warning for vector's non-initializing constructor [Open]

> Username: kylelutz  
> Created at: 2014-12-29 19:09:02 UTC  
> Updated at: 2017-03-21 04:14:51 UTC  
> Url: https://github.com/boostorg/compute/issues/382  

Add a better warning to the `size_t` constructor for `vector<T>` which documents its non-initializing behavior.  
  
From the review:  
  
```  
<http://kylelutz.github.io/compute/boost/compute/vector.html> the  
unusual behaviour of vector's size_t constructor (not initializing the  
elements) should be warned more prominently.  
```  
##
