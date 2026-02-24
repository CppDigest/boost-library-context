# #115 Fix fusion::at<vector<...>, N> for C array types [Merged]

> Username: ccharly  
> Created at: 2015-11-10 10:12:25 UTC  
> Updated at: 2015-11-11 02:02:52 UTC  
> Merged at: 2015-11-11 02:02:52 UTC  
> Closed at: 2015-11-11 02:02:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/115  

Hi there,  
  
It seems that implementing fast `value_at` for vectors breaks compatibility with types that cannot be returned from functions. The following example does not work anymore after this [commit](https://github.com/boostorg/fusion/commit/1ad2e59e072d726abe3dad78fe130af19c1445b8).  
  
``` c++  
#include <boost/fusion/container.hpp>  
#include <iostream>  
  
using seq = boost::fusion::vector<float[4]>;  
using res = boost::fusion::result_of::value_at<seq, boost::mpl::int_<0>>::type;  
  
int main(int argc, char **argv) {  
  return 0;  
}  
```  
  
My fix still uses the O(1) implementation and restore compatibility with those kind of types.  
Let me know if something is wrong with that PR!

---

## Comment 1

> Username: Flast  
> Created_at: 2015-11-10 11:23:28 UTC  
> Url: https://github.com/boostorg/fusion/pull/115#issuecomment-155394036  

Good catch!  
However, unfortunately, some older compiler don't compile `typename decltype(...)::type` style usage: http://melpon.org/wandbox/permlink/gqsETBpfkcWtpsd0. To avoid this issue, insert ugly `mpl::identity` once more: http://melpon.org/wandbox/permlink/oZIT4IVRJcBgy7yL.

---

## Comment 2

> Username: Flast  
> Created_at: 2015-11-10 11:32:57 UTC  
> Url: https://github.com/boostorg/fusion/pull/115#issuecomment-155395928  

Ah, GCC isn't affected. Only Clang 3.0 is affected due to minimal feature requirements.

---

## Comment 3

> Username: ccharly  
> Created_at: 2015-11-10 13:07:21 UTC  
> Url: https://github.com/boostorg/fusion/pull/115#issuecomment-155414728  

Oh ok! I'll fix that then. Thanks for the tip! I was not aware of such problems with old compilers.

---

## Comment 4

> Username: ccharly  
> Created_at: 2015-11-10 13:37:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/115#issuecomment-155420771  

This is now fixed. Do you have any other suggestions?

---

## Comment 5

> Username: Flast  
> Created_at: 2015-11-10 14:10:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/115#issuecomment-155430924  

LGTM.  
@djowel how about this PR?

---

## Comment 6

> Username: djowel  
> Created_at: 2015-11-10 17:39:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/115#issuecomment-155504873  

Do we have to support "older" compilers? The fix is ugly indeed.

---

## Comment 7

> Username: Flast  
> Created_at: 2015-11-11 01:26:49 UTC  
> Url: https://github.com/boostorg/fusion/pull/115#issuecomment-155625120  

I think have to do if it is possible and able to maintain with trivial cost.

---

## Comment 8

> Username: djowel  
> Created_at: 2015-11-11 02:02:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/115#issuecomment-155631275  

OK.

---
