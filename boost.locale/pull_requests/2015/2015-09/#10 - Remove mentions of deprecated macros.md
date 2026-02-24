# #10 Remove mentions of deprecated macros [Closed]

> Username: mclow  
> Created at: 2015-09-11 02:55:17 UTC  
> Updated at: 2015-10-18 15:43:27 UTC  
> Closed at: 2015-10-18 15:43:27 UTC  
> Url: https://github.com/boostorg/locale/pull/10  

In Boost 1.51 the macros `BOOST_NO_CHAR16_T` and `BOOST_NO_CHAR32_T` were deprecated, and the replacements `BOOST_NO_CXX11_CHAR16_T` and `BOOST_NO_CXX11_CHAR32_T` wee introduced.    
  
I would like to remove the deprecated macros, so this change uses them instead of the old names.  
  
No functionality change.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-10-13 16:40:14 UTC  
> Url: https://github.com/boostorg/locale/pull/10#issuecomment-147771531  

Ping?

---

## Comment 2

> Username: artyom-beilis  
> Created_at: 2015-10-14 05:01:09 UTC  
> Url: https://github.com/boostorg/locale/pull/10#issuecomment-147935735  

It is little bit complicated than that.  
- I need to check that every relevant header/source includes config.  
- I need to check that stuff actually works  
  
Last time I tested C++11 char16/32_t support was exceptionally poor - not only characters  
required but also relevant std::locale facets - which are usually the problem.  
  
Probably I'll need to go to internal boost-locale char16/32 config.

---

## Comment 3

> Username: mclow  
> Created_at: 2015-10-14 05:35:54 UTC  
> Url: https://github.com/boostorg/locale/pull/10#issuecomment-147940132  

The only change here is to use a different macro - and the two were defined to be the same (and I'm pretty sure that they come from the same header).

---

## Comment 4

> Username: artyom-beilis  
> Created_at: 2015-10-14 05:43:53 UTC  
> Url: https://github.com/boostorg/locale/pull/10#issuecomment-147941229  

It is opt-in vs opt-out case  
  
I need to test it deeply before doing the change - mostly make sure the config is correct.  
  
It is in my TODO list for near future

---

## Comment 5

> Username: artyom-beilis  
> Created_at: 2015-10-18 15:43:03 UTC  
> Url: https://github.com/boostorg/locale/pull/10#issuecomment-149025745  

As I thought - the "replacement" macros are for no   
use for Boost.Locale  
  
Boost.Locale needs both having these characters  
and having standard library support for them  
like std::num_punct facet, etc.  
  
The problem is that it is defined in compiler config  
and it does not answer on library issue.  
  
Trying to build Boost.Locale with "updated" in C++11 mode  
macros cause multiple issues due to missing facet  
  
So I replaced the macros with BOOST_LOCALE_ENABLE_CHAR(16|32)_T  
that can be defined by user if he wants to try C++11 support  
  
So deprecated macro was removed but the alternative   
isn't suitable replacement at all.   
  
Artyom Beilis  
  
> The only change here is to use a different macro - and  
> the two were defined to be the same (and I'm pretty   
> sure that they come from the same header).>—  
> Reply to this email directly or view it on GitHub.

---
