# #266 - review size limits [Open]

> Username: vinniefalco  
> Created at: 2022-07-28 15:20:16 UTC  
> Updated at: 2023-07-18 21:52:50 UTC  
> Url: https://github.com/boostorg/url/issues/266  

Without the table we could use a larger bit width for `off_t`. And we should review every function to make sure it respects the size limits. Te refactoring of the top level rfc rules may have invalidated existing checks for limits.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-03 20:12:27 UTC  
> Url: https://github.com/boostorg/url/issues/266#issuecomment-1236192236  

I removed checks for max_size

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-10-13 16:59:18 UTC  
> Url: https://github.com/boostorg/url/issues/266#issuecomment-1277915641  

We are just working with `std::size_t` now and `off_t` doesn't exist anymore. What's the state of this issue? Does it still make sense?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-10-13 18:14:57 UTC  
> Url: https://github.com/boostorg/url/issues/266#issuecomment-1277996899  

I'm pretty sure there are a few loose ends where we don't correctly check for overflow when performing unsigned arithmetic. I tried to get `edit_segments` and `edit_params` right, maybe you want to look at them and see if they are correct and sufficiently tested?
