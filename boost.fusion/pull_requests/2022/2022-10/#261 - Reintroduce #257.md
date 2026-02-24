# #261 Reintroduce #257 [Merged]

> Username: Kojoley  
> Created at: 2022-10-10 02:35:53 UTC  
> Updated at: 2022-10-18 21:28:54 UTC  
> Merged at: 2022-10-11 02:28:12 UTC  
> Closed at: 2022-10-11 02:28:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/261  



---

## Comment 1

> Username: djowel  
> Created_at: 2022-10-10 02:42:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/261#issuecomment-1272725189  

@denzor200 I'm going to merge this after checks have completed. But please comment if you will.

---

## Review 2 [Changes requested]

> Username: denzor200  
> Created_at: 2022-10-10 05:20:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/fusion/pull/261#pullrequestreview-1135484303  

📁 include/boost/fusion/view/transform_view/detail/deref_data_impl.hpp

```diff
  18 | #include <boost/utility/result_of.hpp>
  19 | #include <boost/mpl/if.hpp>
  20 |+ #include <boost/type_traits/add_const.hpp>
```

> Username: denzor200  
> Created_at: 2022-10-10 05:13:50 UTC  
> Updated_at: 2022-10-10 05:20:57 UTC  
> Url: https://github.com/boostorg/fusion/pull/261#discussion_r990917697  

redundantly changes in deref_data_impl.hpp


---

## Comment 3

> Username: Kojoley  
> Created_at: 2022-10-18 18:12:17 UTC  
> Url: https://github.com/boostorg/fusion/pull/261#issuecomment-1282813189  

@djowel could you please merge develop to master to include this in the next release?

---

## Comment 4

> Username: djowel  
> Created_at: 2022-10-18 21:28:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/261#issuecomment-1283026359  

Merged.

---
