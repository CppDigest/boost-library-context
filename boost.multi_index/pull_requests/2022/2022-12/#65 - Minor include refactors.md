# #65 Minor include refactors [Merged]

> Username: fanquake  
> Created at: 2022-12-07 16:52:23 UTC  
> Updated at: 2022-12-28 11:35:27 UTC  
> Merged at: 2022-12-28 11:34:58 UTC  
> Closed at: 2022-12-28 11:34:59 UTC  
> Url: https://github.com/boostorg/multi_index/pull/65  

This refactor just replaces includes, with more direct includes. In all cases, we are swapping a header that includes an additional header, for just the additional header, i.e `boost/utility/declval.hpp`, for `boost/type_traits/declval.hpp`.

---

## Comment 1

> Username: joaquintides  
> Created_at: 2022-12-07 17:14:04 UTC  
> Url: https://github.com/boostorg/multi_index/pull/65#issuecomment-1341299573  

Hi, thanks for your contribution. Some observations:  
  
* I don't think `<boost/detail/call_traits.hpp>` is documented anywhere. AFAICS, `<boost/call_traits.hpp>` is the right header to use.  
* The rest of proposed changes look fine to me.

---

## Comment 2

> Username: fanquake  
> Created_at: 2022-12-07 17:19:51 UTC  
> Url: https://github.com/boostorg/multi_index/pull/65#issuecomment-1341307343  

> AFAICS, <boost/call_traits.hpp> is the right header to use.  
  
Ok. Have dropped this commit.

---

## Comment 3

> Username: joaquintides  
> Created_at: 2022-12-07 17:20:54 UTC  
> Url: https://github.com/boostorg/multi_index/pull/65#issuecomment-1341308761  

Perfect, thank you. Unfortunately the window for Boost 1.81 is already closed, so this will go in Boost 1.82.

---
