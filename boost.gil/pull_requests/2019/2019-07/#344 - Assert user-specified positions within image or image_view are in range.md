# #344 Assert user-specified positions within image or image_view are in range [Merged]

> Username: mloskot  
> Created at: 2019-07-21 21:40:12 UTC  
> Updated at: 2019-07-22 17:26:41 UTC  
> Merged at: 2019-07-22 17:26:38 UTC  
> Closed at: 2019-07-22 17:26:38 UTC  
> Url: https://github.com/boostorg/gil/pull/344  

### Description  
  
These assertions validating user-specified x and y values for pixel positions at run-time should help GIL users to catch basic mistakes as early as possible, especially during complex processing algorithms.  
(Easy to make a mistake calling `view(y, x)` instead of `view(x, y)`!)  
  
The checks are deliberately implemented using assertions as debug-only tools disabled in `NDEBUG` builds, and not as exceptions to avoid potentially significant performance hit at run-time in optimised builds.  
  
Add `TODO` comments where certain assumptions are not immediately obvious and may require further testing to clarify and documenting. For example, what are constraints on requesting locators with negative offsets using `image_view::xy_at`?  
  
Tidy up image.hpp and image_view.hpp formatting vertically rather than horizontally with very long lines, align return statements to left for immediate display what is calculated and returned, instead of hiding it behind the right margin.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve  
  
-----  
  
> (Easy to make a mistake calling `view(y, x)` instead of `view(x, y)`!)  
  
Those assertions, if present, would help me and @miralshah365 avoid spending substantial amount of time on investigating what causes `image` heap corruption in one of processing algorithms we've been testing recently.

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-07-22 17:01:24 UTC  
> Url: https://github.com/boostorg/gil/pull/344#issuecomment-513868251  

@stefanseefeld I'd like to have this merged, soonish, unless you have strong objections. Details, if anything necessary, can be tweaked in future, I think.

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-07-22 17:26:01 UTC  
> Url: https://github.com/boostorg/gil/pull/344#issuecomment-513877156  

Thank you @stefanseefeld

---
