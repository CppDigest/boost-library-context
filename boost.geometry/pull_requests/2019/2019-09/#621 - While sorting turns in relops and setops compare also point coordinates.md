# #621 While sorting turns in relops and setops compare also point coordinates. [Merged]

> Username: awulkiew  
> Created at: 2019-09-21 23:59:57 UTC  
> Updated at: 2019-10-25 12:16:54 UTC  
> Merged at: 2019-10-25 12:16:54 UTC  
> Closed at: 2019-10-25 12:16:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/621  

This is a fix for issue https://github.com/boostorg/geometry/issues/619  
  
Currently only fractions are used to check if points are equal in `relate::turns::less` predicate passed to `std::sort` to sort turns before analysis in relops and setops. This may give wrong results in some cases.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-09-23 08:39:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/621#pullrequestreview-291629373  

Thanks for the fix.   
  
Side note: the same bug appears in geographic cs and is fixed by this PR. However, in the case of geographic cs the spherical strategies (e.g. `spherical_segments<>` and not `geographic_segments<>`) are used even if the user explicitly declares `geographic_segments<>` as the desired strategy in the `difference` algorithm.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-09-23 13:47:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/621#issuecomment-534106709  

@vissarion The default intersection strategy for geographic geometries is spherical strategy. If the user explicitly passes geographic intersection strategy then this strategy should be used. Internally spherical strategies could be derived from it if the operation is the same in geographic and spherical CS. E.g. when comparing points for equality. But if geographic intersection strategy is not used after it is passed then this is a bug.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2019-10-21 20:55:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/621#issuecomment-544704090  

@barendgehrels What do you think about this?

---
