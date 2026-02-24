# #483 - Remove remains of Boost.MPL [Closed]

> Username: mloskot  
> Created at: 2020-04-17 17:24:32 UTC  
> Updated at: 2021-02-02 09:18:08 UTC  
> Closed at: 2021-02-02 09:18:04 UTC  
> Url: https://github.com/boostorg/gil/issues/483  

Follow-up to https://github.com/boostorg/gil/pull/474#pullrequestreview-394100204, we should run another review and remove remains of Boost.MPL e.g. `#include <boost/mp11/mpl.hpp>`

---

## Comment 1

> Username: Cypre55  
> Created at: 2021-01-17 19:11:54 UTC  
> Url: https://github.com/boostorg/gil/issues/483#issuecomment-761863967  

Hi,   
  
I would like to work on this.   
I shall be on it now

---

## Comment 2

> Username: Cypre55  
> Created at: 2021-01-17 19:56:22 UTC  
> Url: https://github.com/boostorg/gil/issues/483#issuecomment-761870075  

had doubt regarding what all headers constitutes boost.MPL.  
I have tried searching docs.  
Please point to me where I can find out about this.  
I have found only two locations where #include <boost/mp11/mpl.hpp> is present

---

## Comment 3

> Username: sdebionne  
> Created at: 2021-01-18 09:00:48 UTC  
> Url: https://github.com/boostorg/gil/issues/483#issuecomment-762097251  

```  
$ git grep mpl.hpp  
include/boost/gil/detail/mp11.hpp:#include <boost/mp11/mpl.hpp> // required by dynamic_image and boost::variant (?)  
test/core/pixel/test_fixture.hpp:#include <boost/mp11/mpl.hpp> // for compatibility with Boost.Test  
```  
  
 - `dynamic_image` use Boost.Variant2 which is not based on Boost.MPL  
 - Boost.Test is not used anymore but `lightweight_test` from Boost.Core   
  
So there is a chance that these two includes can be safely removed. You could setup your dev env for running the tests and give it a try.  
  
There are also other indirect uses of Boost.MPL (such as Boost.Iterator) that need to be tracked down and removed but that's probably not part of the `good-first-issue`.

---

## Comment 4

> Username: mloskot  
> Created at: 2021-01-18 10:46:43 UTC  
> Url: https://github.com/boostorg/gil/issues/483#issuecomment-762162576  

> There are also other indirect uses of Boost.MPL (such as Boost.Iterator) that need to be tracked down and removed but that's probably not part of the `good-first-issue`.  
  
Yes, I agree.

---

## Comment 5

> Username: mloskot  
> Created at: 2021-02-02 09:18:04 UTC  
> Url: https://github.com/boostorg/gil/issues/483#issuecomment-771490800  

Fixed by #543
