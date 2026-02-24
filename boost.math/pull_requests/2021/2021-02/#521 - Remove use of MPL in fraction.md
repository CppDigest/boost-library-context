# #521 Remove use of MPL in fraction [Merged]

> Username: mborland  
> Created at: 2021-02-06 19:15:42 UTC  
> Updated at: 2021-02-09 18:02:36 UTC  
> Merged at: 2021-02-07 10:37:11 UTC  
> Closed at: 2021-02-07 10:37:11 UTC  
> Url: https://github.com/boostorg/math/pull/521  

Now that C++11 will be the minimum requirement the remaining uses of boost::mpl can be removed. Additional minor changes to use the C++11 standards and style.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-02-06 19:49:21 UTC  
> Url: https://github.com/boostorg/math/pull/521#issuecomment-774532179  

Thanks Matt, looks fine.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-02-07 10:04:31 UTC  
> Updated_at: 2021-02-07 10:21:11 UTC  
> Url: https://github.com/boostorg/math/pull/521#issuecomment-774646389  

@jzmaddock CI is clean. Should be good to go.  
  
I do not see drone listed in the checks performed. Is it set to only run on merge/commit to develop?

---
