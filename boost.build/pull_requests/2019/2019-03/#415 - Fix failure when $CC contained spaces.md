# #415 Fix failure when $CC contained spaces [Closed]

> Username: pdimov  
> Created at: 2019-03-24 17:20:39 UTC  
> Updated at: 2021-10-02 20:59:59 UTC  
> Closed at: 2019-05-06 14:52:33 UTC  
> Url: https://github.com/boostorg/build/pull/415  



---

## Comment 1

> Username: pdimov  
> Created_at: 2019-03-25 01:39:05 UTC  
> Url: https://github.com/boostorg/build/pull/415#issuecomment-476026104  

```  
build.jam:187: Unescaped special character in argument $(CC:J= )  
```  
  
That's overly pedantic; the `:` is inside `()`, so it shouldn't be interpreted as a separator.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2019-03-25 01:47:40 UTC  
> Url: https://github.com/boostorg/build/pull/415#issuecomment-476027310  

AMDG  
  
On 3/24/19 7:39 PM, Peter Dimov wrote:  
> ```  
> build.jam:187: Unescaped special character in argument $(CC:J= )  
> ```  
>   
> That's overly pedantic; the `:` is inside `()`, so it shouldn't be interpreted as a separator.  
>   
  
It's not the ':'.  The reason that this happens is  
probably because of the space.  This would previously  
have been ill-formed because it was parsed as "$(CC:J=" ")"  
I admit the message is a bit misleading here and is  
probably unnecessary.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: khourig  
> Created_at: 2019-04-22 15:39:01 UTC  
> Url: https://github.com/boostorg/build/pull/415#issuecomment-485452935  

So close.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2019-05-06 14:52:33 UTC  
> Url: https://github.com/boostorg/build/pull/415#issuecomment-489650150  

This is now obsolete, as the build scripts have been rewritten for building with CXX/C++.

---

## Comment 5

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:58 UTC  
> Url: https://github.com/boostorg/build/pull/415#issuecomment-932819724  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
