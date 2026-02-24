# #18 Fix compile error with optional<optional<T>&> [Closed]

> Username: Houndie  
> Created at: 2016-05-13 16:01:01 UTC  
> Updated at: 2016-10-27 13:50:00 UTC  
> Closed at: 2016-10-27 13:10:05 UTC  
> Url: https://github.com/boostorg/optional/pull/18  

Enable_if was discluding optional types in optional's constructor and  
other key functions.  This had the side effect of preventing optionals  
of optionals.  
  
See Trac #12203

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2016-05-19 06:35:58 UTC  
> Url: https://github.com/boostorg/optional/pull/18#issuecomment-220239638  

Thanks for the patch. I will need some time to test it and check if it doesn't break other thins.

---

## Comment 2

> Username: Houndie  
> Created_at: 2016-05-19 12:50:21 UTC  
> Url: https://github.com/boostorg/optional/pull/18#issuecomment-220314463  

Please do...it's only lightly tested.

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2016-10-27 13:10:05 UTC  
> Url: https://github.com/boostorg/optional/pull/18#issuecomment-256635938  

I am now closing this unmerged pull request. I provided a slightly different fix in develop branch. It will make it into 1.63 Boost release.

---

## Comment 4

> Username: Houndie  
> Created_at: 2016-10-27 13:50:00 UTC  
> Url: https://github.com/boostorg/optional/pull/18#issuecomment-256646151  

Sounds good, thanks for fixing it!

---
