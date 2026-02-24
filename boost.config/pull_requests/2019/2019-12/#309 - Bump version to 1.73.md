# #309 Bump version to 1.73 [Merged]

> Username: mclow  
> Created at: 2019-12-11 18:36:26 UTC  
> Updated at: 2022-11-20 17:43:51 UTC  
> Merged at: 2019-12-11 19:27:24 UTC  
> Closed at: 2019-12-11 19:27:24 UTC  
> Url: https://github.com/boostorg/config/pull/309  



---

## Comment 1

> Username: pdimov  
> Created_at: 2019-12-14 04:37:53 UTC  
> Url: https://github.com/boostorg/config/pull/309#issuecomment-565682288  

Can you please merge this to master as well? Thanks.

---

## Comment 2

> Username: tanzislam  
> Created_at: 2019-12-15 02:43:38 UTC  
> Url: https://github.com/boostorg/config/pull/309#issuecomment-565771858  

I'm having MSVC unable to find `libboost_date_time-vc142-mt-s-x64-1_72.lib` at link-time on a clean checkout of the current "master" (f6756cb88) of the super-project. (`.\b2 --layout=versioned toolset=msvc variant=release threading=multi runtime-link=static --with-date_time` had produced a `libboost_date_time-vc142-mt-s-x64-1_73.lib`, but the application still expects a v1.72 library via this header.)  
  
Getting this patch to the "master" branch (and updating the submodule reference in the "master" branch of the super-project) should fix this. (Confirmed by checking out the "develop" branch of this submodule and rebuilding.)

---
