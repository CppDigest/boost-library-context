# #568 Fixed nth_element, added unary_nor [Open]

> Username: koosha94  
> Created at: 2016-03-08 03:39:31 UTC  
> Updated at: 2018-02-24 20:34:38 UTC  
> Url: https://github.com/boostorg/compute/pull/568  

This commit adds unary_nor, and nor1 as a logical operator.  
It also adds/fixes support for user defined types in nth_element.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-03-08 10:27:19 UTC  
> Updated_at: 2016-03-08 18:26:43 UTC  
> Url: https://github.com/boostorg/compute/pull/568#issuecomment-193709360  

Thanks for that! It's a good idea, but I have some comments:  
- NOR is a binary operator (negation of OR).   
- NOR operator can be constructed just using `binary_negate` and `logial_or`:  
  
``` cpp  
not2(logical_or(arg1, arg2))  
```  
- Don't use `master` branch for pull requests, make a special feature branch (in this case it could be called `fix-nth-element' or whatever), because now your`master` and Boost.Compute are different and that can make problems when you want to make next pull request.   
- It would be great to have test for user-defined types for nth_element.  
  
Maybe it would good to add `find` algorithm version that works with user defined types (`Compare` or `Equal` binary function would be required)?

---
