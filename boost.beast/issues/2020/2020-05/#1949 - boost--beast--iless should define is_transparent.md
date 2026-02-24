# #1949 - boost::beast::iless should define is_transparent [Closed]

> Username: s13n  
> Created at: 2020-05-09 15:45:08 UTC  
> Updated at: 2020-06-12 19:20:32 UTC  
> Closed at: 2020-06-12 19:20:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1949  

In order to use the `iless` predicate with `std::map` (for example), it would be very helpful to have a definition of `is_transparent` included, so that heterogenous key comparisons work.  
  
See also https://en.cppreference.com/w/cpp/container/map/find  
  
specifically the signatures (3) and (4).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-09 15:45:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1949#issuecomment-626195106  

seems reasonable

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-06-09 01:31:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1949#issuecomment-640974827  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-06-09 07:28:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1949#issuecomment-641089955  

> This issue has been open for a while with no activity, has it been resolved?  
  
No Activity? I submitted a PR 4 days ago.  
  
We need to upgrade this robot.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-06-09 14:50:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1949#issuecomment-641348236  

>We need to upgrade this robot.  
  
Yeah the app is called "Stale Bot" and it should refresh the inactivity timer when another issue or pull request references the stale issue. This is the author: https://github.com/bkeepers
