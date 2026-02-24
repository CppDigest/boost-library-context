# #10 add option to exclude files from scan [Merged]

> Username: HDembinski  
> Created at: 2020-04-30 22:47:23 UTC  
> Updated at: 2022-07-14 11:16:00 UTC  
> Merged at: 2022-07-14 11:15:59 UTC  
> Closed at: 2022-07-14 11:16:00 UTC  
> Url: https://github.com/boostorg/bcp/pull/10  

Adds an option to exclude paths from module scan. I need this for Boost.Histogram, which depends on a large part of boost only indirectly through tests and examples. When copying histogram, I don't want to include these dependencies.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2020-05-26 11:04:39 UTC  
> Url: https://github.com/boostorg/bcp/pull/10#issuecomment-633957780  

Hi, could I get a review for this patch?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-07-14 11:15:54 UTC  
> Url: https://github.com/boostorg/bcp/pull/10#issuecomment-1184320870  

So sorry, just didn't see this till now.... Oh well only 2 years late!  Looks good to me, will merge!

---
