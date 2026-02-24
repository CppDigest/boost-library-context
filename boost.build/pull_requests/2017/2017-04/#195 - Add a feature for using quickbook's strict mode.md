# #195 Add a feature for using quickbook's strict mode [Merged]

> Username: danieljames  
> Created at: 2017-04-25 20:58:09 UTC  
> Updated at: 2021-10-02 22:18:38 UTC  
> Merged at: 2017-05-09 13:51:27 UTC  
> Closed at: 2017-05-09 13:51:27 UTC  
> Url: https://github.com/boostorg/build/pull/195  

I've just added strict mode to develop in the quickbook module.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2017-04-25 21:19:42 UTC  
> Url: https://github.com/boostorg/build/pull/195#issuecomment-297168294  

FYI.. Other changes that depend on this PR broke the CI build https://circleci.com/gh/boostorg/boost/3499

---

## Comment 2

> Username: danieljames  
> Created_at: 2017-04-25 21:30:29 UTC  
> Url: https://github.com/boostorg/build/pull/195#issuecomment-297170983  

I've already reverted it:  
  
https://github.com/boostorg/quickbook/commit/0223ebb9385452699b335887e4e510843e4204f8  
  
On 25 April 2017 at 22:19, Rene Rivera <notifications@github.com> wrote:  
  
> FYI.. Other changes that depend on this PR broke the CI build  
> https://circleci.com/gh/boostorg/boost/3499  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/build/pull/195#issuecomment-297168294>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AAAdd5lsjMXgWn0wBpn1UvMfBybnc8v0ks5rzmNugaJpZM4NIDA2>  
> .  
>

---
