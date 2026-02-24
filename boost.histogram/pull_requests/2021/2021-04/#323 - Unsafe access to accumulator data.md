# #323 Unsafe access to accumulator data [Open]

> Username: HDembinski  
> Created at: 2021-04-27 11:20:25 UTC  
> Updated at: 2022-02-08 16:22:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/323  

- [ ] Test this with downstream boost-histogram

---

## Comment 1

> Username: HDembinski  
> Created_at: 2021-04-27 11:22:18 UTC  
> Url: https://github.com/boostorg/histogram/pull/323#issuecomment-827531192  

@henryiii Before I change all accumulators in this way, we should try first whether this change works the way we expect in boost-histogram. I can try to make the changes to test this in boost-histogram.

---

## Comment 2

> Username: henryiii  
> Created_at: 2021-08-19 13:58:23 UTC  
> Url: https://github.com/boostorg/histogram/pull/323#issuecomment-901938982  

Can this be rebased on top of current develop?

---

## Comment 3

> Username: HDembinski  
> Created_at: 2021-09-26 10:32:30 UTC  
> Url: https://github.com/boostorg/histogram/pull/323#issuecomment-927280280  

@henryiii Rebased on develop as you requested

---

## Comment 4

> Username: henryiii  
> Created_at: 2021-09-30 16:46:01 UTC  
> Url: https://github.com/boostorg/histogram/pull/323#issuecomment-931490649  

Thanks, I'll work on this further soon - might be after Python 3.10 / pybind11 2.8 / Windows 11 though, all to be released next Monday. (I don't think Windows 11 affects me, but recently noticed it had the same release date as Python 3.10) First initial try showed it will take some work, but didn't get far enough for good feedback yet.

---

## Comment 5

> Username: henryiii  
> Created_at: 2022-02-08 16:22:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/323#issuecomment-1032806632  

FYI, I did attempt this a little while ago, but it will take a bit of work to properly implement. Boost-histogram has to know the memory layout exactly, since it can cast between a block of Python memory to C++ without copy. Convincing pybind11 of this without access to the actual structure just requires manual work for things normally covered by macros. I'll try to get back to this in ~1 week.

---
