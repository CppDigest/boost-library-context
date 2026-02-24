# #28 - Not clear how to make a function object pipable [Closed]

> Username: viboes  
> Created at: 2015-10-04 00:51:58 UTC  
> Updated at: 2015-10-30 22:50:42 UTC  
> Closed at: 2015-10-30 22:50:42 UTC  
> Url: https://github.com/boostorg/hof/issues/28  

It is not clear if the two previous lines are needed to make `sum` pipable.  
  
``` c++  
FIT_STATIC_FUNCTION(sum) = sum_f();  
```  
  
and  
  
``` c++  
const constexpr pipable_adaptor<sum_f> sum = {};  
```  
  
It would be great to have a link to the complete example in the example/tutorial directory.  
  
BTW, why `const constexpr`?

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-30 22:50:42 UTC  
> Url: https://github.com/boostorg/hof/issues/28#issuecomment-152668789  

I have rewritten the quick start guide to make this clearer.
