# #217 - Incorrect inline documentation for the upper parser [Closed]

> Username: dominicprice  
> Created at: 2025-03-23 21:48:35 UTC  
> Updated at: 2025-03-30 21:07:48 UTC  
> Closed at: 2025-03-30 21:07:48 UTC  
> Url: https://github.com/boostorg/parser/issues/217  

Just bringing up a typo in the comment above the `upper` parser, which currently reads  
  
https://github.com/boostorg/parser/blob/6414f99e04d362182fa5d3ba87750c993db7e969/include/boost/parser/parser.hpp#L7832-L7836  
  
The class `Lu` is right, but `lower case` should read `upper case`

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-03-25 03:52:14 UTC  
> Url: https://github.com/boostorg/parser/issues/217#issuecomment-2750000105  

Thanks for reporting this!  We just kissed the deadline for the current Boost release, but I'll try to have this fixed in the next one.
