# #542 Fix incorrect count update in rngs [Merged]

> Username: jszuppe  
> Created at: 2015-12-18 21:22:54 UTC  
> Updated at: 2015-12-19 17:27:48 UTC  
> Merged at: 2015-12-19 17:05:45 UTC  
> Closed at: 2015-12-19 17:05:45 UTC  
> Url: https://github.com/boostorg/compute/pull/542  

In `generate` methods in `mersenne_twister_engine`, `linear_congruential_engine` and `threefry_engine` classes there are incorrect updates of count variables. They leads to assigning out-of-range in used kernels. (`#include <algorithm>` is added for `std::min`.)  
  
In `linear_congruential_engine` there is incorrect `seed` method call: there is no `seed` method with just one parameter of type result_type.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-12-19 17:05:46 UTC  
> Url: https://github.com/boostorg/compute/pull/542#issuecomment-166004063  

Thanks for catching these. Merged!

---
