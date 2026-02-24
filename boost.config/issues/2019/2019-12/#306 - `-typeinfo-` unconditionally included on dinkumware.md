# #306 - `<typeinfo>` unconditionally included on dinkumware [Open]

> Username: Kojoley  
> Created at: 2019-12-02 16:17:06 UTC  
> Updated at: 2020-12-13 20:14:09 UTC  
> Url: https://github.com/boostorg/config/issues/306  

It was added in 668b3fccaef7460036a1a83a9c90df8cbe76c4ab to fix lexical_cast (https://svn.boost.org/trac10/ticket/4115) problem, but that seems to be an overkill (and probably not needed since https://github.com/boostorg/lexical_cast/commit/3ce36a28484b76fab2dd96e8ee43f52ae87d1353?).  
Also, it is a strange thing that with defined `BOOST_NO_STD_TYPEINFO` there is `std::typeinfo`. It completely undermines the purpose of the macro and of `boost/core/typeinfo.hpp`.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-12-02 18:37:39 UTC  
> Updated at: 2019-12-02 18:37:50 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-560524284  

I have reproduced the https://svn.boost.org/trac10/ticket/4115 problem by applying https://github.com/boostorg/lexical_cast/pull/31 and https://github.com/boostorg/config/pull/307 on VC9.0/10.0/11.0, the fix for LexicalCast proposed in https://github.com/boostorg/lexical_cast/pull/32.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-12-03 18:26:44 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-561295442  

I'm curious to know why you think injecting typeinfo into the namespace in which it really should be defined anyway is such a bad idea?  
Though we should certainly restrict the workaround to pre-msvc-12, as it's only required for obsolete compilers anyway.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-12-03 18:42:54 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-561301717  

I did not say it is a bad idea, it is convenient but costly.

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2020-03-22 23:53:14 UTC  
> Updated at: 2020-03-23 22:22:39 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-602300433  

The IAR compiler for Arm uses Dinkumware and if I disable exceptions, which is admittedly I guess a non-standard extension, then it fails to compile this injection because it does not have `::typeinfo`.  
So, I would be in favour of limiting this kind of thing to only the compilers where it is known to work, rather than assuming it works in general.

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2020-07-08 03:12:39 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-655256255  

Digging around more on this, I'm not sure that #307 is the right solution after all.  
  
I fixed this another way by a very simple modification to `dinkumware.hpp` on line 89, which currently is:  
  
`#if BOOST_MSVC < 1800`  
  
but I think it _should_ be:  
  
`#if defined(_MSC_VER) && BOOST_MSVC < 1800`  
  
What do you think, @jzmaddock? I believe you added that line 6 months ago.  :)

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-07-08 07:25:13 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-655339556  

You're correct: that's supposed to detect old MSVC versions, checking for `defined(BOOST_MSVC)` should do the trick also?

---

## Comment 7

> Username: Kojoley  
> Created at: 2020-07-08 13:21:45 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-655515991  

#307 is the right way, because Boost.TypeInfo is a portable solution to the problem the code is solving.

---

## Comment 8

> Username: Kojoley  
> Created at: 2020-07-08 13:27:03 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-655519254  

> You're correct: that's supposed to detect old MSVC versions, checking for defined(BOOST_MSVC) should do the trick also?  
  
`defined(__ghs__)` is clearly detecting something different than MSVC

---

## Comment 9

> Username: glenfe  
> Created at: 2020-07-08 13:28:31 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-655520070  

> You're correct: that's supposed to detect old MSVC versions, checking for `defined(BOOST_MSVC)` should do the trick also?  
  
Yes to `defined(BOOST_MSVC)` since the whole point of `BOOST_MSVC` is avoid checking `_MSC_VER` which MSVC-pretenders define.

---

## Comment 10

> Username: jeremy-murphy  
> Created at: 2020-07-09 04:26:52 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-655890964  

> You're correct: that's supposed to detect old MSVC versions, checking for `defined(BOOST_MSVC)` should do the trick also?  
  
Sure, I was just suggesting what I already saw used repeatedly within that file, but now I realize that they were testing the version of `_MSC_VER` whereas this is `BOOST_MSVC`.  
I trust you all know these macros better than I do.

---

## Comment 11

> Username: jeremy-murphy  
> Created at: 2020-07-09 04:42:02 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-655895461  

Even though we might agree about that line 89 needing a `defined(BOOST_MSVC)`, it is actually tangential to this issue, which is about unconditionally including `<typeinfo>`, when there is an existing cross-library solution for it in Boost.Core.  
  
I'm inclined to agree with @Kojoley that their solution is the right way to go, but I think the PR needs to also remove all the references to `BOOST_NO_STD_TYPEINFO` from the tests, etc? Since I think Dinkumware is the only place that defines it.  
  
So, I would be super happy about someone putting the `defined(BOOST_MSVC)` in line 89 in the interim, to at least fix the broken conditional.

---

## Comment 12

> Username: GregKon  
> Created at: 2020-12-13 20:14:09 UTC  
> Url: https://github.com/boostorg/config/issues/306#issuecomment-744061959  

>   
>   
> Even though we might agree about that line 89 needing a `defined(BOOST_MSVC)`, it is actually tangential to this issue, which is about unconditionally including `<typeinfo>`, when there is an existing cross-library solution for it in Boost.Core.  
>   
> I'm inclined to agree with @Kojoley that their solution is the right way to go, but I think the PR needs to also remove all the references to `BOOST_NO_STD_TYPEINFO` from the tests, etc? Since I think Dinkumware is the only place that defines it.  
>   
> So, I would be super happy about someone putting the `defined(BOOST_MSVC)` in line 89 in the interim, to at least fix the broken conditional.  
  
I does confirm problem (IAR) and possibly solution works.
