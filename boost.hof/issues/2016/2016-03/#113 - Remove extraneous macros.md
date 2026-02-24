# #113 - Remove extraneous macros [Closed]

> Username: pfultz2  
> Created at: 2016-03-05 17:07:09 UTC  
> Updated at: 2016-03-23 18:36:40 UTC  
> Closed at: 2016-03-23 00:03:33 UTC  
> Url: https://github.com/boostorg/hof/issues/113  

`FIT_UNARY_PERFECT_ID` is never used. And `FIT_NO_CONSTEXPR_VOID` should not be defined to `1`.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-22 23:51:58 UTC  
> Url: https://github.com/boostorg/hof/issues/113#issuecomment-200083332  

There is still the `FIT_HAS_RVALUE_THIS` macro.
