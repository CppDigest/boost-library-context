# #87 win32: compile fix for msvc2015 [Merged]

> Username: timblechmann  
> Created at: 2016-06-20 09:11:55 UTC  
> Updated at: 2017-03-01 10:40:41 UTC  
> Merged at: 2016-06-28 12:19:10 UTC  
> Closed at: 2016-06-28 12:19:10 UTC  
> Url: https://github.com/boostorg/thread/pull/87  

i had to use this patch to compile against msvc2015

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2016-06-28 12:26:08 UTC  
> Url: https://github.com/boostorg/thread/pull/87#issuecomment-229033847  

@timblechmann @viboes That's strange as there is no (_MSC_VER > 1900). MSVC 14 (2015) as well as the latest MSVC 15 have _MSC_VER defined as 1900.

---

## Comment 2

> Username: Smibu  
> Created_at: 2016-12-10 17:07:44 UTC  
> Updated_at: 2017-03-01 10:40:41 UTC  
> Url: https://github.com/boostorg/thread/pull/87#issuecomment-266223103  

@MarcelRaad @timblechmann @viboes This PR appears to break things on VS 2017 RC. For VS 2017 RC, the compiler version is 19.10.24629, so `_MSC_VER` is `1910`. That means `on_tls_prepare` (and the two other functions) will have return type `void`, but the CRT apparently expects a zero value on success.  
  
I encountered this issue when I built my application on VS 2017 RC, and when I ran it, it failed silently. As I debugged it, I noticed `on_tls_prepare` returning an undefined (nonzero) value, and as a result, the program quitted immediately.  
  
I fixed this for myself by reverting the change made in this PR.  
  
Update: This issue has been fixed in https://github.com/boostorg/thread/pull/114.

---
