# #390 - Fix documentation for BOOST_COMPUTE_ADAPT_STRUCT [Open]

> Username: kylelutz  
> Created at: 2014-12-29 19:17:14 UTC  
> Updated at: 2017-03-21 04:14:46 UTC  
> Url: https://github.com/boostorg/compute/issues/390  

Update the documentation for `BOOST_COMPUTE_ADAPT_STRUCT` to indicate that it must be called at global scope.  
  
From the review:  
  
```  
BOOST_COMPUTE_ADAPT_STRUCT should state that it must be used at global  
scope (i.e. not within a function or other namespace).  
```  
##
