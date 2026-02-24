# #532 Ensure cpp_dec_float throws on malformed string input. [Merged]

> Username: jzmaddock  
> Created at: 2023-02-14 19:37:29 UTC  
> Updated at: 2023-02-18 08:40:35 UTC  
> Merged at: 2023-02-16 17:02:05 UTC  
> Closed at: 2023-02-16 17:02:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/532  

Fixes https://github.com/boostorg/multiprecision/issues/499.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-02-17 18:29:13 UTC  
> Updated_at: 2023-02-17 18:29:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/532#issuecomment-1435074019  

Hey guys, in an unrelated project on my nightlies, I just found [this one](https://github.com/ckormanyos/wide-decimal/actions/runs/4199996131/jobs/7285554313#step:5:24) on C++14 in some kind of GCC ox x64.  
  
looks like a missing header that might booby trap on a release on some down the line... Fix it now. i do not know where/which header, but want to give heads up.  
  
Cc: @jzmaddock and @mborland

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-02-17 19:11:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/532#issuecomment-1435117785  

>Hey guys, in an unrelated project on my nightlies, I just found [this one](https://github.com/ckormanyos/wide-decimal/actions/runs/4199996131/jobs/7285554313#step:5:24) on C++14 in some kind of GCC ox x64.  
looks like a missing header that might booby trap on a release on some down the line... Fix it now. i do not know where/which header, but want to give heads up.  
  
Ugh, should be fixed now.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2023-02-18 08:40:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/532#issuecomment-1435522671  

>> looks like a missing header that might booby trap on a release...  
  
> should be fixed now.  
  
Thanks John (@jzmaddock). Yes it is fixed. My nightly on an unrelated project ran green.

---
