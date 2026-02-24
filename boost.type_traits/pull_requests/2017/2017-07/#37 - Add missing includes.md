# #37 Add missing includes [Merged]

> Username: pdimov  
> Created at: 2017-07-01 20:58:30 UTC  
> Updated at: 2017-07-17 11:26:50 UTC  
> Merged at: 2017-07-07 07:11:33 UTC  
> Closed at: 2017-07-07 07:11:33 UTC  
> Url: https://github.com/boostorg/type_traits/pull/37  

This reorders the includes in the tests to always include the header being tested first, to check for self-sufficiency, then fixes the issues uncovered by that.  
  
Thanks to @apolukhin for the report.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-07-05 20:15:20 UTC  
> Url: https://github.com/boostorg/type_traits/pull/37#issuecomment-313214117  

This PR fixes a few errors with missing includes. Most annoying (for me) is a missing `<boost/config.hpp>` include in conditional.hpp that leads to compilation failures on GCC and CLang in pre-C++11 modes.  
  
Please merge.

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-07-05 20:28:59 UTC  
> Url: https://github.com/boostorg/type_traits/pull/37#issuecomment-313217590  

The Appveyor fails are because of `<warnings-as-errors>on` and warning C4800. Not sure how this worked before, but I don't think it has to do with this PR.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-07-05 20:40:23 UTC  
> Url: https://github.com/boostorg/type_traits/pull/37#issuecomment-313220409  

Ah, it's because 4800 is disabled in `test.hpp`, so `has_prefix_operator` now emits them.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-07-05 21:06:12 UTC  
> Url: https://github.com/boostorg/type_traits/pull/37#issuecomment-313226971  

Fixed.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-07-05 23:34:20 UTC  
> Url: https://github.com/boostorg/type_traits/pull/37#issuecomment-313254819  

Travis and Appveyor all green.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2017-07-07 07:11:44 UTC  
> Url: https://github.com/boostorg/type_traits/pull/37#issuecomment-313605702  

Thanks for this Peter.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-07-17 11:26:50 UTC  
> Url: https://github.com/boostorg/type_traits/pull/37#issuecomment-315729748  

John, it may be a good idea to merge this to master before the release goes out, or if not, at least apply the fix to `conditional.hpp`.

---
