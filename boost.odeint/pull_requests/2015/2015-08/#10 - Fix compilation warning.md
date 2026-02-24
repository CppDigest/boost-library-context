# #10 Fix compilation warning [Merged]

> Username: nasailja  
> Created at: 2015-08-18 18:11:53 UTC  
> Updated at: 2015-08-22 17:13:06 UTC  
> Merged at: 2015-08-22 17:12:40 UTC  
> Closed at: 2015-08-22 17:12:40 UTC  
> Url: https://github.com/boostorg/odeint/pull/10  

warning: struct template 'reference_wrapper' was previously declared as a class template [-Wmismatched-tags]  
include/boost/core/ref.hpp:59:25: note: previous use is here

---

## Comment 1

> Username: headmyshoulder  
> Created_at: 2015-08-19 05:01:00 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132446936  

Which compiler / c++ standard library are you using? If I remember correctly, we have changed this definition several times for different standard libraries.

---

## Comment 2

> Username: mariomulansky  
> Created_at: 2015-08-19 08:34:58 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132493333  

Yes, I have changed that back and forth already and both versions produced this warning for me. So indeed it would be good to know for which compiler this now works? And also, which version of boost  you are using?

---

## Comment 3

> Username: ddemidov  
> Created_at: 2015-08-19 08:40:52 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132494422  

Looks like this should be in sync with https://github.com/boostorg/core/blob/master/include/boost/core/ref.hpp#L59, which is indeed declared as class in v1.56 through v1.59 (no older versions on github).

---

## Comment 4

> Username: mariomulansky  
> Created_at: 2015-08-19 08:51:27 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132497856  

Hrm yes, in my stdlib it is also defined as a class. So it also seems to me this should be the correct way.

---

## Comment 5

> Username: headmyshoulder  
> Created_at: 2015-08-19 09:07:08 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132502616  

It might be different with the clang standard lib, or gcc's standard  
library or even boost. I am not sure if we can fix this completely  
without checking compiler versions. For me, it would also be find to  
ignore this warning.  
  
On 19.08.2015 10:51, Mario Mulansky wrote:  
  
> Hrm yes, in my stdlib it is also defined as a class. So it also seems to  
> me this should be the correct way.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/odeint/pull/10#issuecomment-132497856.

---

## Comment 6

> Username: mariomulansky  
> Created_at: 2015-08-19 09:25:20 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132507336  

Maybe we will have warning with some compiler either way, but there is also no real argument for not merging this pull request. And as Denis points out, this should then be compatible with boost, which makes sense in my view.

---

## Comment 7

> Username: nasailja  
> Created_at: 2015-08-19 12:30:27 UTC  
> Updated_at: 2015-08-19 12:31:13 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132573576  

I'm using boost 1.59.0 and:  
Apple LLVM version 6.0 (clang-600.0.57) (based on LLVM 3.5svn)  
Target: x86_64-apple-darwin13.4.0  
Thread model: posix  
and stdlib from gcc 4.2 I guess.

---

## Comment 8

> Username: nasailja  
> Created_at: 2015-08-19 12:38:21 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132576518  

reference_wrapper is a class at least in http://open-std.org/JTC1/SC22/WG21/docs/papers/2015/n4527.pdf#subsection.20.9.4

---

## Comment 9

> Username: MarcelRaad  
> Created_at: 2015-08-19 12:46:15 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132578260  

As this is a forward declaration of boost::reference_wrapper, which is always a class, standard library implementations don't matter, do they?

---

## Comment 10

> Username: mariomulansky  
> Created_at: 2015-08-19 15:13:23 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132634494  

I think when I changed it previously I ran into the same error in some cases, maybe because boost::reference wrapper is than simply implemented by lifting std::reference_wrapper into the boost namespace... However, this could have changed by now.  
Nasaija: have you checked compiling with both C++03 and C++11 ?

---

## Comment 11

> Username: nasailja  
> Created_at: 2015-08-19 15:24:57 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132637614  

I get the warning with c++11 and don't get it with c++03.

---

## Comment 12

> Username: mariomulansky  
> Created_at: 2015-08-19 18:54:59 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132741347  

and with the fix the warnings disappears for both cases.

---

## Comment 13

> Username: headmyshoulder  
> Created_at: 2015-08-20 08:12:53 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-132928272  

On 19.08.2015 20:55, Mario Mulansky wrote:  
  
> and with the fix the warnings disappears for both cases.  
  
Ok, then let's merge it. I just wanted to say that we can not fix this  
for all times, since our code depends on code we do not maintain.  
  
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/odeint/pull/10#issuecomment-132741347.

---

## Comment 14

> Username: mariomulansky  
> Created_at: 2015-08-22 17:13:05 UTC  
> Url: https://github.com/boostorg/odeint/pull/10#issuecomment-133727831  

ok, merged.  
Thanks a lot for the PR.

---
