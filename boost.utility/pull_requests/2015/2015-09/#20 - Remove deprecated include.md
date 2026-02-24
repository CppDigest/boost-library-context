# #20 Remove deprecated include [Merged]

> Username: MarcelRaad  
> Created at: 2015-09-11 14:28:44 UTC  
> Updated at: 2015-09-12 11:41:30 UTC  
> Merged at: 2015-09-11 16:06:38 UTC  
> Closed at: 2015-09-11 16:06:38 UTC  
> Url: https://github.com/boostorg/utility/pull/20  

All that boost/iterator.hpp does is pull std::iterator into namespace boost. A comment in that header mentions: "This header is obsolete and will be deprecated."

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-09-11 14:36:15 UTC  
> Updated_at: 2015-09-11 14:37:00 UTC  
> Url: https://github.com/boostorg/utility/pull/20#issuecomment-139562701  

boost/iterator.hpp also includes cstddef for std::ptrdiff_t. boost/operators.hpp doesn't include cstddef on its own but uses std::ptrdiff_t. Please, add #include <cstddef>.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-09-11 15:57:58 UTC  
> Url: https://github.com/boostorg/utility/pull/20#issuecomment-139583892  

Thanks, fixed now!

---
