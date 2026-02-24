# #413 Fix non-cartesian compare function objects (bg::less, bg::equal_to, bg::greater). [Merged]

> Username: awulkiew  
> Created at: 2017-08-05 00:33:00 UTC  
> Updated at: 2017-08-30 11:57:21 UTC  
> Merged at: 2017-08-30 11:39:57 UTC  
> Closed at: 2017-08-30 11:39:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/413  

CHANGES:  
  
This PR fixes the predicates for non-cartesian CSes and improves consistency. It adds support for poles and antimeridians in non-cartesian coordinate systems and use of `math::equals()` also in single-Dimension versions (consistency, also with bg::equals()). Objects of different types can now be compared (C++14).  
  
Compare strategies are redesigned to make them consistent with other strategies in the library.  
  
Because coordinates are now always compared with `math::equals()` the predicates cannot be used in expand/envelope for expansion of a Box. They are not needed anyway because support for spherical equatorial and geographic CSes is implemented explicitly. I added proper implementation of expand(Box, Pt) for spherical polar, previously it was falling into default/cartesian using bg::less<> specialized for spherical polar (btw it was not specialized for equatorial nor geographic).  
  
Support for spherical polar in utils for normalization of non-cartesian coordinates is added.  
  
`detail::relate::less<>` is no longer needed so it's removed and replaced with `bg::less<> in various algorithms`.  
  
PROPOSAL:  
  
I'd like to move these function objects from `policies/compare.hpp` because AFAIU these are not policies. E.g. to `util/compare.hpp` or `util/functional.hpp` (`std::` function objects are implemented in `<functional>`).  
  
CONCERNS:  
  
`bg::less<>` and `bg::greater<>` are used in `buffer()` with `check_monotonic`. This probably won't work properly in non-cartesian coordinate systems. But this PR doesn't change anything in this aspect because `buffer()` currently only works in cartesian CS.  
  
Since single-Dimension versions are used in `buffer()` this means that the results may change, i.e. now coordinates are compared with `math::equals()` before less-comparison. Previously standalone `std::less`/`std::greater` was called (see: https://github.com/boostorg/geometry/commit/fdca32a6e5d6f624a53b0f8b1190390edd42531e#diff-2140f14aaee322f1e6642ac55b7c8abdL110). So if this is desired then e.g. `geometry::less<>` should be simply replaced with `std::less<>` (`buffer` works only in cartesian CS). All of the tests pass for me so my guess is that it's ok.

---

## Comment 1

> Username: vissarion  
> Created_at: 2017-08-29 10:35:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/413#issuecomment-325624397  

Looks ok to me. Thanks.

---
