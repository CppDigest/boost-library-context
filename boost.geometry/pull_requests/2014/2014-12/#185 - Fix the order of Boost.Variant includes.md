# #185 [geometry] Fix the order of Boost.Variant includes. [Merged]

> Username: awulkiew  
> Created at: 2014-12-15 18:16:12 UTC  
> Updated at: 2014-12-31 13:50:08 UTC  
> Merged at: 2014-12-15 22:46:13 UTC  
> Closed at: 2014-12-15 22:46:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/185  

This PR fixes the errors related to Boost.Variant variadic templates support.  
  
variant_fwd.hpp must be included before other Boost.Variant headers because it contains config used in other parts of this library.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2014-12-16 18:26:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/185#issuecomment-67206873  

> variant_fwd.hpp must be included before other Boost.Variant headers because it contains config used in other parts of this library.  
  
That's strange. Neither `<boost/variant/static_visitor.hpp>` nor `<boost/variant/apply_visitor.hpp>` require `<boost/variant/variant_fwd.hpp` to be included. Could you please give some example where this issue appear?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-12-16 18:48:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/185#issuecomment-67210266  

This commit added support for variadic templates in apply_visitor_delayed.hpp: https://github.com/boostorg/variant/commit/0231eefd434e0c862be2886904c696e9b24a9e6e. The code is conditionally enabled if `BOOST_VARIANT_DO_NOT_USE_VARIADIC_TEMPLATES` is not defined. This macro is defined automatically in vector_fwd.hpp e.g. if `BOOST_NO_CXX11_VARIADIC_TEMPLATES` is defined. Without this header included before apply_visitor.hpp the code containing variadic templates is always compiled.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2014-12-17 08:25:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/185#issuecomment-67290688  

Thanks for the hint! Fixed the issue in https://github.com/boostorg/variant/commit/7c8b70cb9d6076fcc2601b0f3de0acef1cbdcb32 . Will merge it to the master branch in a few days

---

## Comment 4

> Username: brunolalande  
> Created_at: 2014-12-20 22:09:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/185#issuecomment-67751711  

Thanks Antony for fixing the root cause. Adam, I think this means you can roll back your change to return to original ordering (which was alphabetical). Or I can do it if you don't have the time, just let me know.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2014-12-21 11:00:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/185#issuecomment-67766713  

Actually the order was not alphabetical (`static_visitor.hpp`, `apply_visitor.hpp`, `variant_fwd.hpp`), do you remember why? I may of course reorder the headers however I see the currently used order as a "safe approach" since this is how those headers are included by `boost/variant.hpp`. Though it's not elegant indeed.

---

## Comment 6

> Username: brunolalande  
> Created_at: 2014-12-31 09:43:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/185#issuecomment-68432516  

Hi Adam,  
  
You're correct, I hadn't seen that. Probably a mistake. I would personally  
order them alphabetically, in general a library should not require a  
particular order of inclusion so ordering this way "just in case" of an  
issue with Boost.Variant doesn't seem to bring much value... I'd almost say  
the opposite, it's good to be able to detect such regression in a Boost  
library as early as possible.  
  
Regards  
Bruno  
  
On Sun, Dec 21, 2014 at 11:00 AM, Adam Wulkiewicz notifications@github.com  
wrote:  
  
> Actually the order was not alphabetical (static_visitor.hpp,  
> apply_visitor.hpp, variant_fwd.hpp), do you remember why? I may of course  
> reorder the headers however I see the currently used order as a "safe  
> approach" since this is how those headers are included by  
> boost/variant.hpp. Though it's not elegant indeed.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/geometry/pull/185#issuecomment-67766713.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-12-31 13:50:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/185#issuecomment-68442636  

Yes and in particular AFAIU it should be possible to include Boost.Variant lower-level headers separately. So indeed it'd be good to detect any errors. I'll reorder the includes.

---
