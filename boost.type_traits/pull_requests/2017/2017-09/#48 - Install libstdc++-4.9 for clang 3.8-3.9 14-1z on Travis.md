# #48 Install libstdc++-4.9 for clang 3.8/3.9 14/1z on Travis [Closed]

> Username: pdimov  
> Created at: 2017-09-07 12:40:59 UTC  
> Updated at: 2019-11-02 11:41:24 UTC  
> Closed at: 2017-10-25 18:52:16 UTC  
> Url: https://github.com/boostorg/type_traits/pull/48  

This should fix the `::gets` errors caused by the more recent `glibc` on Trusty.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-09-07 17:12:34 UTC  
> Url: https://github.com/boostorg/type_traits/pull/48#issuecomment-327864565  

On 07/09/2017 13:40, Peter Dimov wrote:  
>  
> This should fix the |::gets| errors caused by the more recent |glibc|   
> on Trusty.  
>  
  
Thanks, there's a workaround for this in Boost.Config already, but the   
problem test header wasn't including it, I've just pushed a fix for that   
and we'll see...

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-10-25 18:52:16 UTC  
> Url: https://github.com/boostorg/type_traits/pull/48#issuecomment-339433450  

This looks to be fixed.

---
