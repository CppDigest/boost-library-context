# #209 Add <boost/gil/assert.hpp> [Closed]

> Username: mloskot  
> Created at: 2019-01-12 10:08:54 UTC  
> Updated at: 2020-03-15 16:17:55 UTC  
> Closed at: 2019-07-25 07:27:23 UTC  
> Url: https://github.com/boostorg/gil/pull/209  

Add proxy header as single place of `<boost/assert.hpp>` include.  
In future, it should simplify replacing `BOOST_ASSERT` with  
custom `BOOST_GIL_ASSERT`.  
  
### References  
  
Refines #96, #208 following discussion in https://github.com/boostorg/gil/pull/208#issuecomment-453695771 with @stefanseefeld   
  
### Tasklist  
  
- [ ] Review  
- [ ] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-01-12 22:56:48 UTC  
> Updated_at: 2019-01-12 23:05:15 UTC  
> Url: https://github.com/boostorg/gil/pull/209#issuecomment-453787331  

@stefanseefeld Regarding possibility to remove dependency on `<boost/assert.hpp>`, it is not easy, if not possible at all. The dependency graph is this: Boost.Assert < Boost.Core < GIL.  
So, removing `#include <boost/assert.hpp>` do not buy us anything really.  
Do you agree?  
  
A sort of flexibility seems possible with `#define BOOST_GIL_ASSERT BOOST_ASSERT` that could be disabled/enabled independently. For example, disable `BOOST_GIL_ASSERT` but keep `BOOST_ASSERT` enabled. But, I'm not entirely convinced such flexibility would be needed or expected at all.  
What do you think?

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-01-12 23:03:01 UTC  
> Url: https://github.com/boostorg/gil/pull/209#issuecomment-453787687  

OK, fair enough. Thanks for investigating !

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-07-25 07:27:23 UTC  
> Url: https://github.com/boostorg/gil/pull/209#issuecomment-514930492  

For the following reasons, I'm closing this as initial brainstorm of the idea  
  
- Sole addition of `boost/gil/assert.hpp` does not improve much over using  `boost/assert.hpp`  
- Benefits of custom `BOOST_GIL_ASSERT` are not as obvious as it initially seems  
- Introducing new `BOOST_GIL_ASSERT` used/enabled/disabled in parallel to `BOOST_ASSERT` seems like increased complexity, but without clear benefits  
  
If the idea is still compelling, it needs more thought.

---
