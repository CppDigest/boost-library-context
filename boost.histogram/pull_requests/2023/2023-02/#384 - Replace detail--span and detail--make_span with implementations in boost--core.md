# #384 Replace detail::span and detail::make_span with implementations in boost::core [Merged]

> Username: HDembinski  
> Created at: 2023-02-18 14:28:32 UTC  
> Updated at: 2023-02-19 16:42:03 UTC  
> Merged at: 2023-02-19 16:42:02 UTC  
> Closed at: 2023-02-19 16:42:02 UTC  
> Url: https://github.com/boostorg/histogram/pull/384  



---

## Comment 1

> Username: HDembinski  
> Created_at: 2023-02-18 15:01:51 UTC  
> Url: https://github.com/boostorg/histogram/pull/384#issuecomment-1435695198  

@glenfe It works like a charm now. Thanks!

---

## Comment 2

> Username: glenfe  
> Created_at: 2023-02-18 16:04:28 UTC  
> Url: https://github.com/boostorg/histogram/pull/384#issuecomment-1435706700  

@HDembinski great.   Is `boost::make_span` (from `<boost/core/make_span.hpp>`) OK too?

---

## Comment 3

> Username: HDembinski  
> Created_at: 2023-02-19 13:54:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/384#issuecomment-1435995576  

> @HDembinski great. Is `boost::make_span` (from `<boost/core/make_span.hpp>`) OK too?  
  
Thanks for the pointer; I missed that you also added make_span, works also fine!

---

## Comment 4

> Username: glenfe  
> Created_at: 2023-02-19 16:41:04 UTC  
> Url: https://github.com/boostorg/histogram/pull/384#issuecomment-1436034646  

Looks good; thanks @HDembinski

---
