# #208 Replace assert() macro with BOOST_ASSERT() macro [Merged]

> Username: mloskot  
> Created at: 2019-01-11 23:50:55 UTC  
> Updated at: 2019-03-29 20:29:13 UTC  
> Merged at: 2019-01-12 09:21:02 UTC  
> Closed at: 2019-01-12 09:21:02 UTC  
> Url: https://github.com/boostorg/gil/pull/208  

Add `#include <boost/assert.hpp>` where necessary.  
Apply minor clean-up near the macro replacements.  
  
### References  
  
Closes #96  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-01-11 23:57:01 UTC  
> Url: https://github.com/boostorg/gil/pull/208#issuecomment-453694965  

@stefanseefeld not sure if there is anything exciting to review here, but I requested it to keep you in loop.

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-01-12 00:01:13 UTC  
> Updated_at: 2019-01-12 00:03:41 UTC  
> Url: https://github.com/boostorg/gil/pull/208#issuecomment-453695771  

Yeah, thanks. The change looks simple enough, but I'm struggling to figure out in what way exactly `BOOST_ASSERT` is an improvement over `assert`. The document you reference doesn't tell (other than a vague "is more configurable").  
(I don't have any strong objection, other than a simple "why introduce a new Boost dependency ?", so if there are good reasons other than "because some document says so", I'm OK with the change.)

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-01-12 00:11:12 UTC  
> Url: https://github.com/boostorg/gil/pull/208#issuecomment-453697479  

https://www.boost.org/doc/libs/1_69_0/libs/assert/doc/html/assert.html  
> If the macro BOOST_DISABLE_ASSERTS is defined (...) BOOST_ASSERT(expr) expands to ((void)0)  
  
> why introduce a new Boost dependency ?  
  
I just thought Boost.Assert is such an ubiquitous Boost dependency, that avoiding it is minor win.  
  
I agree, it's not ideal though.  
In near future, we can have another look, copy `BOOST_ASSERT` as `BOOST_GIL_ASSERT`. That will just take automatic search & replace of `assert.hpp` to `gil/assert.hpp` and the macro itself.

---
