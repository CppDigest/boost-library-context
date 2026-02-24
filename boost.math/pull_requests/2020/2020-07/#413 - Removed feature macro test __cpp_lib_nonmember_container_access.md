# #413 Removed feature macro test __cpp_lib_nonmember_container_access [Closed]

> Username: kamleshbhalui  
> Created at: 2020-07-28 16:39:02 UTC  
> Updated at: 2024-02-22 10:27:15 UTC  
> Closed at: 2024-02-22 10:27:15 UTC  
> Url: https://github.com/boostorg/math/pull/413  

Removed feature macro test __cpp_lib_nonmember_container_access  
since it is available from c++20.  
Used __cplusplus version instead.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-07-29 10:22:08 UTC  
> Url: https://github.com/boostorg/math/pull/413#issuecomment-665578822  

I'm not convinced this is the right fix - or indeed that a fix is needed, rationale:  
  
* While these macros were formally part of the std from C++20, they were originally tracked as the SD6 extensions from C++14 onwards and with broad vendor support.  
* __cpp_lib_nonmember_container_access may be (and I'm pretty sure is) be defined by pre-C++17 compilers which supported this feature prior to C++17 being formally released.  
* While not applicable to this PR, there are cases where compilers even post C++20 may not implement even C++11 or 14 features if the underlying OS or hardware makes them non-applicable (threads/futures and similar on real time OSes for example), in these cases checking the feature test macro is the correct thing to do _that's what they're for_.  
  
So if there's something other than older MSVC versions which aren't supporting the SD6 feature test macros from C++14 on up, I would probably rather work around those compilers/std libraries than not use these macros for their intended purpose.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-07-30 17:59:38 UTC  
> Updated_at: 2020-07-30 18:00:00 UTC  
> Url: https://github.com/boostorg/math/pull/413#issuecomment-666566258  

Another opportunity for me to reflect on how dumb it was for me to use `std::size`.  
  
Can we just make a backwards incompatible change and get rid of it?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-07-31 17:12:39 UTC  
> Url: https://github.com/boostorg/math/pull/413#issuecomment-667235599  

> Can we just make a backwards incompatible change and get rid of it?  
  
It's an implementation detail, you can do whatever you like to it ;)  
  
But the more I look at the code, the less of an issue this looks to me.  I'd like to here from @kamleshbhalui why this is a problem.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-07-31 17:51:38 UTC  
> Url: https://github.com/boostorg/math/pull/413#issuecomment-667252958  

> It's an implementation detail, you can do whatever you like to it ;)  
  
Wait, I thought we had a requirement you could call `std::size` on the container; my backwards incompatible change would be to require a `.size()` member. . .

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-07-31 17:54:38 UTC  
> Url: https://github.com/boostorg/math/pull/413#issuecomment-667254312  

> Wait, I thought we had a requirement you could call std::size on the container; my backwards incompatible change would be to require a .size() member. . .  
  
Sure but we have a workaround for std::size not supported, which does the same thing as `std::size`.  But like I said, this looks like a non-issue to me.

---

## Comment 6

> Username: kamleshbhalui  
> Created_at: 2020-08-01 13:01:37 UTC  
> Updated_at: 2020-08-01 13:02:19 UTC  
> Url: https://github.com/boostorg/math/pull/413#issuecomment-667528635  

> > Can we just make a backward-incompatible change and get rid of it?  
>   
> It's an implementation detail, you can do whatever you like to it ;)  
>   
> But the more I look at the code, the less of an issue this looks to me. I'd like to here from @kamleshbhalui why this is a problem.  
  
@jzmaddock my thought towards this change was about standard conformance, but I agree with your rationale.  
Since this feature is formally provided in c++17, so instead of removing the macro __cpp_lib_nonmember_container_access test completely.  
what do you say about adding one more check i.e.  
  __cpluplus>=201703 || defined(__cpp_lib_nonmember_container_access) ?

---
