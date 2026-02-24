# #131 Introduce feature-test macro BOOST_NO_CXX17_STD_RANDOM_SHUFFLE [Merged]

> Username: DanielaE  
> Created at: 2017-04-09 07:03:36 UTC  
> Updated at: 2017-04-22 11:22:51 UTC  
> Merged at: 2017-04-17 17:43:33 UTC  
> Closed at: 2017-04-17 17:43:33 UTC  
> Url: https://github.com/boostorg/config/pull/131  

The algorithm std::random_shuffle was marked as deprecated in C++14 and is removed from the upcoming C++17 standard. The name itself is still reserved and library implementations may continue to ship it. In this sense, support for std::random_shuffle must be regarded as purely optional and a feature-test macro is required to test for its presence.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: glenfe  
> Created_at: 2017-04-15 18:42:12 UTC  
> Url: https://github.com/boostorg/config/pull/131#issuecomment-294310933  

I wonder if this should instead be named 'BOOST_NO_CXX17_DEPRECATED_STD_RANDOM_SHUFFLE' or something similar. Or some convention established to indicate features deprecated rather than features not yet available.

---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-04-15 19:58:48 UTC  
> Url: https://github.com/boostorg/config/pull/131#issuecomment-294314682  

Good question, Glen! I'm in favour of differentiating between a feature not yet (or not sufficiently) available, and a feature being removed after deprecation. OTOH, there is BOOST_NO_AUTO_PTR which is repurposed now for the absence of the deprecated std::auto_ptr in msvc's library in c++17 mode, even though it is documented as a c++03 defect macro. But that shouldn't prevent us from looking for a better naming scheme for features removed from the standard.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-04-16 07:40:42 UTC  
> Url: https://github.com/boostorg/config/pull/131#issuecomment-294338538  

On 15/04/2017 19:42, Glen Fernandes wrote:  
>  
> I wonder if this should instead be named   
> 'BOOST_NO_CXX17_DEPRECATED_STD_RANDOM_SHUFFLE' or something similar.   
> Or some convention established to indicate features deprecated rather   
> than features not yet available.  
>  
  
It's not just deprecated, it's removed.  And yes we have a few of these   
- std::unary/binary_function in particular are used all over Boost I   
suspect.  
  
I like the BOOST_NO/BOOST_HAS convention because it keeps everything   
simple and reasonably obvious, but I guess if we were being systematic,   
this would be something like BOOST_<STD>_REMOVED_<FEATURE> ?  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 4

> Username: DanielaE  
> Created_at: 2017-04-16 08:20:28 UTC  
> Url: https://github.com/boostorg/config/pull/131#issuecomment-294339932  

So, that would lead to either  
  
- BOOST_NO_AUTO_PTR  
- BOOST_NO_RANDOM_SHUFFLE  
- BOOST_NO_BINDERS_ADAPTERS  
  
or  
  
- BOOST_CXX17_REMOVED_AUTO_PTR  
- BOOST_CXX17_REMOVED_RANDOM_SHUFFLE  
- BOOST_CXX17_REMOVED_BINDERS_ADAPTERS  
  
What's your pick?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2017-04-16 10:57:50 UTC  
> Url: https://github.com/boostorg/config/pull/131#issuecomment-294345759  

I've asked on the mailing list, we need to establish a convention once   
and for all for this.  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2017-04-17 08:07:01 UTC  
> Url: https://github.com/boostorg/config/pull/131#issuecomment-294428638  

As per discussion on the list, can we change this one to BOOST_NO_CXX98_RANDOM_SHUFFLE, rationale:  
  
* Name should reflect the std that introduced the feature.  
* There's no precedent for including "STD_" in the macro name - everything is in std:: anyway.  
  
Thanks!  John.

---

## Comment 7

> Username: DanielaE  
> Created_at: 2017-04-17 11:51:26 UTC  
> Url: https://github.com/boostorg/config/pull/131#issuecomment-294471210  

Alright, John - here we go!  
  
Ciao  
   Dani

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2017-04-17 17:44:57 UTC  
> Url: https://github.com/boostorg/config/pull/131#issuecomment-294539149  

Merged, also added tests, and 2 more related macros: BOOST_NO_CXX98_BINDERS and BOOST_NO_CXX98_FUNCTION_BASE.  Moved docs for these into a new section for removed features.

---

## Comment 9

> Username: DanielaE  
> Created_at: 2017-04-18 05:22:47 UTC  
> Url: https://github.com/boostorg/config/pull/131#issuecomment-294685151  

Thanks, John - these would have been the next pull-requests of mine 😀

---
