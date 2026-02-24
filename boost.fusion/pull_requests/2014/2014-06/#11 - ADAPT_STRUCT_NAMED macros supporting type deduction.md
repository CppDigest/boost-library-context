# #11 ADAPT_STRUCT_NAMED macros supporting type deduction [Merged]

> Username: daminetreg  
> Created at: 2014-06-24 22:54:51 UTC  
> Updated at: 2014-06-25 19:57:43 UTC  
> Merged at: 2014-06-25 19:57:43 UTC  
> Closed at: 2014-06-25 19:57:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/11  

This pull-request continues the work of adding type deduction support to the existing ADAPT\* macros, and rework a bit the macros proposed in pull-requests #9 & #10 to handle the special case of adapted struct as views.  
  
This contains  the ADAPT_STRUCT_NAMED macros capable of deducing type and there related documentation and unit-tests.  
  
With these changes all tests pass on llvm/clang, however I'm wondering if the changes are correct to handle proxied object, because I cleaned a bit and removed the specific ADAPT_STRUCT_NAMED_FILLERs macros and now use the PREFIX and IS_VIEW flag to determine how the field have to be accessed in such a case. Is this the right usage for the PREFIX, or was something else thought before ?  
  
Thank you for the time you invest reviewing my little steps,  
Cheers,

---

## Comment 1

> Username: daminetreg  
> Created_at: 2014-06-24 23:04:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/11#discussion_r14161005  

I removed the FILLER macros specific to `adapt_struct_named.hpp`, and by the way handled passing `obj.Y` via the usage of the PREFIX param for  for structs adapted as view.   
  
I believe that the PREFIX exists for this usage, but I may be wrong. That's why I'm asking.  
  
Is this the reason why the PREFIX parameter exists in _BOOST_FUSION_ADAPT_STRUCT_C_BASE_ or am I missing something ?

---
