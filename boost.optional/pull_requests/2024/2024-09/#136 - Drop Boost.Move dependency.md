# #136 Drop Boost.Move dependency [Merged]

> Username: typenametea  
> Created at: 2024-09-28 16:24:00 UTC  
> Updated at: 2024-10-06 22:08:37 UTC  
> Merged at: 2024-10-06 22:07:58 UTC  
> Closed at: 2024-10-06 22:07:58 UTC  
> Url: https://github.com/boostorg/optional/pull/136  

https://github.com/boostorg/optional/issues/134  
- Implement constexpr definitions of forward and move to replace usage of boost::move and boost::forward from the Boost.Move library.  
- Alter tests to use std::move instead of boost::move.  
- Remove the dependency on Boost.Move from build.jam  
  
All unit tests pass on my machine. I also built the superproject again.   
  
I am not certain if I have taken all the necessary steps to 'officially' remove the dependency of Boost.Move, is removing it from the build.jam sufficient?

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2024-09-29 17:03:06 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2381427849  

One other file to change is `doc/92_relnotes.qbk`

---

## Comment 2

> Username: typenametea  
> Created_at: 2024-10-04 20:03:18 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2394487817  

I've updated it, let me know if that's correct

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2024-10-04 22:55:43 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2394767066  

The release notes are fine. Still waiting for the other comments to be addressed.

---

## Comment 4

> Username: typenametea  
> Created_at: 2024-10-04 23:00:34 UTC  
> Updated_at: 2024-10-04 23:01:06 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2394770037  

> The release notes are fine. Still waiting for the other comments to be addressed.  
  
Hmm, I can only see one comment from you regarding the documentation in this pull request 🤔  
  
Did you perhaps not submit the review?

---

## Review 5 [Commented]

> Username: akrzemi1  
> Created_at: 2024-10-04 23:20:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/optional/pull/136#pullrequestreview-2335699639  

📁 include/boost/optional/detail/optional_utility.hpp

```diff
  24 |+ template <class T> inline constexpr T&& forward(typename boost::remove_reference<T>::type&& t) noexcept
  25 |+ {
  26 |+   BOOST_STATIC_ASSERT_MSG(!boost::is_lvalue_reference<T>::value, "Can not forward an rvalue as an lvalue.");
```

> Username: akrzemi1  
> Created_at: 2024-09-29 05:34:59 UTC  
> Updated_at: 2024-10-04 23:20:13 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1779913160  

I think at some point a constexpr function needed to be a single return statement. Without any declarations, like static assert.

> Username: akrzemi1  
> Created_at: 2024-09-29 17:00:29 UTC  
> Updated_at: 2024-10-04 23:20:13 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1780088241  

`www.open-std.org` is down, so I cannot confirm it. I have tested on my compilers in C++11 mode and all tests pass.

> Username: akrzemi1  
> Created_at: 2024-10-05 14:37:21 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1788622235  

Ok, so the C++11 (https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3337.pdf) allows:  
— null statements,  
— static_assert-declarations  
— typedef declarations and alias-declarations that do not define classes or enumerations,  
— using-declarations,  
— using-directives,  
— and exactly one return statement;  
  
I am not sure if `BOOST_STATIC_ASSERT_MSG` fits in any of these groups.

> Username: akrzemi1  
> Created_at: 2024-10-05 14:39:43 UTC  
> Updated_at: 2024-10-05 14:40:12 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1788622615  

Ok, maybe just guard the assertion with `BOOST_NO_CXX11_STATIC_ASSERT`.

> Username: typenametea  
> Created_at: 2024-10-05 17:57:08 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1788654103  

I am not sure of where to look in that standard paper, or if I'd even understand its meaning, so I will trust you.  
  
>Ok, maybe just guard the assertion with BOOST_NO_CXX11_STATIC_ASSERT.  
  
The BOOST_STATIC_ASSERT_MACRO is implemented as such;   
```c++  
#ifndef BOOST_NO_CXX11_STATIC_ASSERT  
#  ifndef BOOST_NO_CXX11_VARIADIC_MACROS  
#     define BOOST_STATIC_ASSERT_MSG( ... ) static_assert(__VA_ARGS__)  
#  else  
#     define BOOST_STATIC_ASSERT_MSG( B, Msg ) static_assert( B, Msg )  
#  endif  
#else  
#     define BOOST_STATIC_ASSERT_MSG( B, Msg ) BOOST_STATIC_ASSERT( B )  
#endif  
```  
Do we still need to guart it with `BOOST_NO_CXX11_STATIC_ASSERT` ?

> Username: akrzemi1  
> Created_at: 2024-10-05 21:19:25 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1788715372  

It is in document https://wg21.link/std11, clause 7.1.5 ([dcl.constexpr]), paragraph 3.  
I may be too cautious, bt just in case there are compilers that support rvalue references and variadic templates but no `static_assert`, do guard the static assert with `BOOST_NO_CXX11_STATIC_ASSERT`. In that case. THe normal `static_assert` will be fine, so:  
  
```c++  
{  
  #ifndef BOOST_NO_CXX11_STATIC_ASSERT  
  static_assert(!boost::is_lvalue_reference<T>::value, "Can not forward an rvalue as an lvalue.");  
  #endif  
  return static_cast<T&&>(t);  
```

---

📁 include/boost/optional/detail/optional_utility.hpp

```diff
   1 |+ // Copyright (C) 2024 Andrzej Krzemienski.
```

> Username: akrzemi1  
> Created_at: 2024-09-29 16:53:57 UTC  
> Updated_at: 2024-10-04 23:20:13 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1780086719  

This is your work. You hold the copyright.

---

📁 include/boost/optional/detail/optional_utility.hpp

```diff
  17 |+ 
  18 |+ // Workaround: forward and move aren't constexpr in C++11
  19 |+ template <class T> inline constexpr T&& forward(typename boost::remove_reference<T>::type& t) noexcept
```

> Username: akrzemi1  
> Created_at: 2024-09-29 17:14:51 UTC  
> Updated_at: 2024-10-04 23:20:13 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1780091067  

Please, stick to the convention in other files, where the template head (`template <...>`) is put on a separate line.


📁 include/boost/optional/optional_io.hpp

```diff
  66 | #ifndef  BOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES
  67 |-       v = boost::move(x);
  67 |+       v = std::move(x);
```

> Username: akrzemi1  
> Created_at: 2024-09-29 17:17:11 UTC  
> Updated_at: 2024-10-04 23:20:13 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1780091497  

This is not a test file. this is part of the implementation. This should be `optional_detail::move()`.

---

📁 include/boost/optional/optional_io.hpp

```diff
  64 |       T x;
  65 |       in >> x;
  66 | #ifndef  BOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES
```

> Username: akrzemi1  
> Created_at: 2024-09-29 17:18:14 UTC  
> Updated_at: 2024-10-04 23:20:13 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1780091679  

This `#ifndef` can be dropped.


---

## Comment 6

> Username: akrzemi1  
> Created_at: 2024-10-04 23:22:03 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2394782568  

Indeed, I haven't.  
Sorry. This is the first time I have been using this.

---

## Comment 7

> Username: typenametea  
> Created_at: 2024-10-05 11:49:17 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2395030397  

> Indeed, I haven't. Sorry. This is the first time I have been using this.  
  
No problem, I've done it many times before myself

---

## Comment 8

> Username: typenametea  
> Created_at: 2024-10-05 11:55:11 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2395031683  

I have a question regarding include guards, for the utility file I simply made mine _BOOST_OPTIONAL_OPTIONAL_DETAIL_OPTIONAL_UTILITY_HPP_ however I notice in other files such as swap _BOOST_OPTIONAL_DETAIL_OPTIONAL_SWAP_AJK_28JAN2015_HPP_ there is an additional part to it, namely initials and a date.   
  
I assume this is to be extra safe in ensuring no one would ever accidently create an identical include guard. Will this be necessary for my utility file too?

---

## Comment 9

> Username: akrzemi1  
> Created_at: 2024-10-05 14:31:48 UTC  
> Updated_at: 2024-10-05 14:40:40 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2395077530  

> I assume this is to be extra safe in ensuring no one would ever accidently create an identical include guard.   
  
Correct.  
  
> Will this be necessary for my utility file too?  
  
I do not think I have been applying it consistently, but given that you ask, I would say _yes_. This can only help.

---

## Review 10 [Commented]

> Username: akrzemi1  
> Created_at: 2024-10-05 14:32:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/optional/pull/136#pullrequestreview-2349614809  

📁 include/boost/optional/detail/optional_utility.hpp

```diff
   1 |- // Copyright (C) 2024 Andrzej Krzemienski.
   1 |+ // Copyright (C) 2024 typenameTea.
```

> Username: akrzemi1  
> Created_at: 2024-10-05 14:32:46 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1788620599  

You prefer to remain anonymous? Ok.

> Username: typenametea  
> Created_at: 2024-10-05 15:06:49 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1788627663  

Somewhat, it's more that this is the online alias I have decided to adopt, and it's somewhat unique compared to my real name. I use this alias in my git config so all of my commits are under 'typenameTea' as well.  I would just prefer to have all of these such things associated with my alias, though I'm happy to share my name in private if you'd like😃   
  
Is it a problem that this is the copyright?

> Username: akrzemi1  
> Created_at: 2024-10-05 20:25:01 UTC  
> Updated_at: 2024-10-05 20:25:02 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1788689806  

My quick research on the Internet indicates that a copyright notice is valid even when your pseudonym is used.  
https://copyrightalliance.org/faqs/can-you-register-copyright-under-pseudonym/  
https://copyrightservice.co.uk/copyright/p03_copyright_notices  
Of course, I am not a lawyer, and stuff found on the Internet is not legally binding, but I consider it good enough.  
It is just a new thing for me.

> Username: vinniefalco  
> Created_at: 2024-10-05 21:01:42 UTC  
> Updated_at: 2024-10-05 21:02:58 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1788713348  

No one can remove the copyright notice, even if you use a pseudonym, because altering the notice is prohibited by the license. Sure... your copyright might expire in ~30 years instead of ~100 years. I'm guessing you probably don't care about this. In practical terms, it doesn't matter. Use what you like.   
  
For more information see:  
  
https://www.copyright.gov/circs/circ32.pdf


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2024-10-05 20:59:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/optional/pull/136#pullrequestreview-2350281052  

📁 doc/92_relnotes.qbk

```diff
  17 | * Dropped dependency on Boost.Utility.
  18 | * Dropped dependency on Boost.Predef.
  19 |+ * Dropped dependency on Boost.Move.
```

> Username: vinniefalco  
> Created_at: 2024-10-05 20:59:59 UTC  
> Url: https://github.com/boostorg/optional/pull/136#discussion_r1788713176  

Hooray :)


---

## Comment 12

> Username: typenametea  
> Created_at: 2024-10-06 19:17:28 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2395550949  

I believe all the review comments have been addressed

---

## Comment 13

> Username: akrzemi1  
> Created_at: 2024-10-06 22:08:36 UTC  
> Url: https://github.com/boostorg/optional/pull/136#issuecomment-2395603137  

Indeed. All tests pass on my compilers.  
Thank you.

---
