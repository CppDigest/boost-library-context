# #175 Fix `empty_value` MSVC bug [Merged]

> Username: k3DW  
> Created at: 2024-06-19 17:42:37 UTC  
> Updated at: 2024-07-16 18:16:33 UTC  
> Merged at: 2024-07-12 12:53:53 UTC  
> Closed at: 2024-07-12 12:53:53 UTC  
> Url: https://github.com/boostorg/core/pull/175  

See [unordered#257](https://github.com/boostorg/unordered/issues/257).  
  
Is this a breaking change in Core? It doesn't break any tests, but is it conceptually wrong?

---

## Comment 1

> Username: mglisse  
> Created_at: 2024-06-22 10:26:27 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2183972673  

(thank you for working on this. Just a user's comments below)  
  
> Is this a breaking change in Core?  
  
I think so. As broken as the permission model is in C++ (private things are traps instead of being invisible), there are still some cases where SFINAE can ignore private things, that work with the current code and would be broken with public inheritance.  
  
> is it conceptually wrong?  
  
Again, I think so. It may still be worth it if the bug it works around is worse than what this breaks (I have no idea how to measure that), but if this change is done, in my opinion it should be protected by something like `BOOST_WORKAROUND(BOOST_MSVC, ...)` with a link to the bug report on Microsoft's website.  
  
On recent compilers, a simpler implementation of empty_value based on (msvc::)no_unique_address could also be considered.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-06-22 16:31:15 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2184090627  

> On recent compilers, a simpler implementation of empty_value based on (msvc::)no_unique_address could also be considered.  
  
This sounds like the best course of action here.

---

## Comment 3

> Username: k3DW  
> Created_at: 2024-06-23 23:06:35 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2185351906  

> > On recent compilers, a simpler implementation of empty_value based on (msvc::)no_unique_address could also be considered.  
>   
> This sounds like the best course of action here.  
  
I'm not sure how far back this problem goes on MSVC. On 16.0, the oldest available version on Compiler Explorer right now, [the issue reproduces](https://godbolt.org/z/jKrohso93).  
  
That said, how would this be implemented using `[[msvc::no_unique_address]]`? Usages of `empty_value` rely on EBO, which is transitive through inheritance. Anything using NUA isn't transitive. [CE link](https://godbolt.org/z/KxMGbeM8G)  
  
I can't find any documentation for when `[[msvc::no_unique_address]]` was introduced. This attribute [doesn't work in MSVC 16.8](https://godbolt.org/z/v7PoG89T6), but it [starts working in MSVC 16.9](https://godbolt.org/z/oczdn4anq). We need this fixed earlier than 16.8, so I don't think we can use NUA here.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-06-23 23:27:23 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2185357784  

[[no_unique_address]] works for the same example under Clang and GCC: https://godbolt.org/z/PzqreT8Eb  
But we need it for MSVC, which is exactly where it doesn't. :-)

---

## Comment 5

> Username: Lastique  
> Created_at: 2024-06-24 00:02:29 UTC  
> Updated_at: 2024-06-24 00:08:19 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2185369462  

Was this bug reported to MSVC devs? If not, please do.  
  
As to this PR, I think, [this](https://godbolt.org/z/dvEecjrPq) is a better workaround. `[[no_unique_address]]` is not worth it. And not the right way to go anyway, as compilers are allowed to ignore/not support attributes, while EBO is a requirement.

---

## Comment 6

> Username: mglisse  
> Created_at: 2024-06-24 06:48:15 UTC  
> Updated_at: 2024-06-25 20:46:21 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2185738480  

> That said, how would this be implemented using `[[msvc::no_unique_address]]`? Usages of `empty_value` rely on EBO, which is transitive through inheritance. Anything using NUA isn't transitive. [CE link](https://godbolt.org/z/KxMGbeM8G)  
  
:exploding_head: The whole point of NUA is to replace EBO, and MSVC managed to fail...  
  
Actually, msvc::no_unique_address is a failure, but there may still be a bit of hope for the real no_unique_address if we complain enough ([report 1](https://developercommunity.visualstudio.com/t/Class-with-a-single-no_unique_address/10506616), [report 2](https://developercommunity.visualstudio.com/t/msvc::no_unique_address-nonconforman/10504173)) before the next ABI break in MSVC :crossed_fingers:

---

## Review 7 [Commented]

> Username: Lastique  
> Created_at: 2024-06-24 08:08:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/175#pullrequestreview-2134988951  

📁 include/boost/core/empty_value.hpp

```diff
  97 | #if !defined(BOOST_NO_TEMPLATE_PARTIAL_SPECIALIZATION)
  98 |+ template<class T>
  99 |+ class empty_value_base
```

> Username: Lastique  
> Created_at: 2024-06-24 08:08:43 UTC  
> Url: https://github.com/boostorg/core/pull/175#discussion_r1650540012  

This class should be in `namespace detail`. And it should only be used `#if defined(BOOST_MSVC)`, otherwise the previous implementation should be in effect.


---

## Comment 8

> Username: Lastique  
> Created_at: 2024-06-24 20:57:59 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2187390355  

Thanks. Looks ok to me.

---

## Comment 9

> Username: k3DW  
> Created_at: 2024-06-25 03:39:54 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2187908156  

> Thanks. Looks ok to me.  
  
Great thanks  
  
> Was this bug reported to MSVC devs? If not, please do.  
  
Got it, [here](https://developercommunity.visualstudio.com/t/Compiler-bug:-Incorrect-C2247-and-C2248/10690025) is the bug report. It seems like it's been around for a while, so I'm not holding out hope that it'll get fixed any time soon. But at least we were able to get a workaround

---

## Comment 10

> Username: Lastique  
> Created_at: 2024-06-25 08:15:06 UTC  
> Updated_at: 2024-06-25 08:18:23 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2188264248  

> > Was this bug reported to MSVC devs? If not, please do.  
>   
> Got it, [here](https://developercommunity.visualstudio.com/t/Compiler-bug:-Incorrect-C2247-and-C2248/10690025) is the bug report.  
  
Please, add a short description and a link to the bug in a comment near `#if defined(BOOST_MSVC)` and the new `test_derived_compile` test.  
  
> It seems like it's been around for a while, so I'm not holding out hope that it'll get fixed any time soon.  
  
If it's not been reported before, it is likely it wasn't known to the MSVC devs, which is why it wasn't fixed and why it needed to be reported. I don't expect it to be immediately fixed, but it will be, eventually.

---

## Comment 11

> Username: joaquintides  
> Created_at: 2024-07-08 07:36:07 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2213250542  

Reminder that we should merge this fix before the Boost 1.86 release window closes. Thank you!

---

## Comment 12

> Username: pdimov  
> Created_at: 2024-07-08 07:39:20 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2213256104  

@glenfe ?

---

## Comment 13

> Username: ashtum  
> Created_at: 2024-07-13 08:44:29 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2226825154  

The following issue has arisen due to a conflict with the new private type alias `base` introduced recently:  
  
```C++  
#include <boost/core/empty_value.hpp>  
#include <memory>  
  
struct A : boost::empty_value<std::allocator<void>>  
{  
};  
  
using base = A;  
  
struct B : base  
{  
  B()  
    : base{}  
  {  
  }  
};  
```  
  
```  
Error (active)	E0265	type "boost::empty_::empty_value<T, N, true>::base [with T=std::allocator<void>, N=0U]" (declared at line 141 of "C:\Users\win\Desktop\boost\boost\core\empty_value.hpp") is inaccessible	  
```

---

## Comment 14

> Username: pdimov  
> Created_at: 2024-07-13 08:46:31 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2226825680  

This can be fixed by renaming `empty_value<..>::base` to something less likely to cause conflicts, such as e.g. `core_ev_base`.

---

## Comment 15

> Username: ashtum  
> Created_at: 2024-07-16 18:16:32 UTC  
> Url: https://github.com/boostorg/core/pull/175#issuecomment-2231527611  

https://github.com/boostorg/core/commit/242b14bb690ca7b4f924b6dd35f397a2779813a3 broke Boost.Beast again. I can rename the typedefs in Beast, but I guess `base_type` is quite a common name used by other users as well: https://github.com/boostorg/beast/blob/develop/test/beast/core/async_base.cpp#L677

---
