# #239 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:25:27 UTC  
> Updated at: 2025-06-28 01:21:13 UTC  
> Merged at: 2025-06-26 23:30:25 UTC  
> Closed at: 2025-06-26 23:30:25 UTC  
> Url: https://github.com/boostorg/date_time/pull/239  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:55:21 UTC  
> Url: https://github.com/boostorg/date_time/pull/239#issuecomment-2295309042  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-06-26 16:52:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/239#issuecomment-3009124161  

Looks like the installation of `software-properties-common` hangs because it waits for user input. Why do we need to install it anyway?

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-06-26 18:47:42 UTC  
> Url: https://github.com/boostorg/date_time/pull/239#issuecomment-3009517731  

gcc-9 is the system compiler on ubuntu 20.04, and gcc-11 is the system compiler on ubuntu-22.04, so just switch to those instead of trying to install them on the previous releases (which doesn't work because the packages aren't present.)

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-06-26 23:31:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/239#issuecomment-3010623041  

I'm merging this as-is, but see https://github.com/boostorg/system/issues/132. Maybe we need to make this header-only as well.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2025-06-27 01:16:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/239#issuecomment-3010986107  

> I'm merging this as-is, but see [boostorg/system#132](https://github.com/boostorg/system/issues/132). Maybe we need to make this header-only as well.  
  
Probably.

---

## Comment 6

> Username: JeffGarland  
> Created_at: 2025-06-28 01:21:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/239#issuecomment-3014798412  

I'd remove the stub -- people have had years to convert now we should make them move on.

---
