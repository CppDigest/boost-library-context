# #2174 removes unused parameter to avoid compiler warning [Closed]

> Username: KnoerleMan  
> Created at: 2021-02-27 10:25:58 UTC  
> Updated at: 2021-03-03 13:42:09 UTC  
> Closed at: 2021-03-03 13:42:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2174  

Removes the name of unused parameter "bytes_transferred" to avoid compiler warnings.  
Related issue #2152

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-02-27 10:33:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2174#issuecomment-787051210  

Thank you!  
  
On Sat, 27 Feb 2021 at 11:26, KnoerleMan <notifications@github.com> wrote:  
  
> Removes the name of unused parameter "bytes_transferred" to avoid compiler  
> warnings.  
> Related issue #2152 <https://github.com/boostorg/beast/issues/2152>  
> ------------------------------  
> You can view, comment on, or merge this pull request online at:  
>  
>   https://github.com/boostorg/beast/pull/2174  
> Commit Summary  
>  
>    - removes unused parameter to avoid compiler warning  
>  
> File Changes  
>  
>    - *M* include/boost/beast/websocket/impl/ssl.hpp  
>    <https://github.com/boostorg/beast/pull/2174/files#diff-21bdb6926741f68c3ecfb56dc280206023f7ff9f2aef3fc73977c5b2473db31c>  
>    (2)  
>  
> Patch Links:  
>  
>    - https://github.com/boostorg/beast/pull/2174.patch  
>    - https://github.com/boostorg/beast/pull/2174.diff  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/2174>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSJCHTPKIM6MVSRGB2TTBDCEJANCNFSM4YJZUVVA>  
> .  
>  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 2

> Username: madmongo1  
> Created_at: 2021-03-03 13:42:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2174#issuecomment-789722095  

Thanks for the PR. Community contributions are very much appreciated.  
I have taken your commit, fixed the compile error and merged into version 308.   
You are credited.  
R

---
