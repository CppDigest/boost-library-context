# #29 - Pragma push reported missing [Closed]

> Username: pabristow  
> Created at: 2020-02-26 11:56:18 UTC  
> Updated at: 2020-02-26 12:35:16 UTC  
> Closed at: 2020-02-26 12:33:38 UTC  
> Url: https://github.com/boostorg/atomic/issues/29  

.\..\..\boost/atomic/detail/memory_order_utils.hpp(45) : warning C4193: #pragma warning(pop) : no matching '#pragma warning(push)'  
  
and I suspect this may be causing trouble by wrongly popping elsewhere?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-02-26 12:35:16 UTC  
> Url: https://github.com/boostorg/atomic/issues/29#issuecomment-591403887  

Thanks.
