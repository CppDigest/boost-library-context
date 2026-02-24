# #801 Change PHOENIX_LIMIT definition in classic/phoenix/tuples.hpp... [Merged]

> Username: pdimov  
> Created at: 2025-05-07 09:52:47 UTC  
> Updated at: 2025-05-09 06:32:13 UTC  
> Merged at: 2025-05-07 23:24:40 UTC  
> Closed at: 2025-05-07 23:24:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/801  

... to match the one in classic/attribute.hpp.  
  
As explained in https://github.com/boostorg/spirit/issues/800, the inconsistent definitions of `PHOENIX_LIMIT` cause ODR violations, e.g. as reported in https://github.com/boostorg/quickbook/issues/27.

---
