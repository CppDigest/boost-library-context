# #59 - Support calling custom functions from lambda expressions [Open]

> Username: kylelutz  
> Created at: 2014-03-10 01:23:12 UTC  
> Updated at: 2017-03-21 04:14:56 UTC  
> Url: https://github.com/boostorg/compute/issues/59  

It should be possible to call custom functions from lambda expressions.  
  
For example, this should be possible:  
  
```  
BOOST_COMPUTE_FUNCTION(float, square, (float x),  
{  
    return x * x;  
});  
  
transform(vec.begin(), vec.end(), vec.begin(), square(2 * _1), queue);  
```  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-29 18:16:39 UTC  
> Url: https://github.com/boostorg/compute/issues/59#issuecomment-68282255  

This has been requested multiple times during the Boost.Compute review. Bumping it up in priority.
