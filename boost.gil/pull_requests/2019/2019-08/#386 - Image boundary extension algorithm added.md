# #386 Image boundary extension algorithm added [Merged]

> Username: lpranam  
> Created at: 2019-08-30 14:39:00 UTC  
> Updated at: 2019-09-14 16:35:36 UTC  
> Merged at: 2019-09-05 18:47:22 UTC  
> Closed at: 2019-09-05 18:47:22 UTC  
> Url: https://github.com/boostorg/gil/pull/386  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
A new algorithm for image boundary extension added.  
This can help with simplifying algorithms like convolution and median filter which is being implemented by @miralshah365.  
  
While extending current `convolve_2d` this will allow all type of convolve options to be accommodated easily and current `convolve_1d` and also use to this to provide a uniform implementation.  
  
### References  
  
#381   
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [X] Add test case(s)  
- [X] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-31 20:06:39 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/386#pullrequestreview-282318418  

The documentation is a bit modest.  
  
I understand these are quite simple operations, but a user may expect to get description of motivation, when to use it (e.g. is it a sort of a method for canvas resize while keeping image content intact), what are (input) limitations if any, etc.

---

## Comment 2

> Username: lpranam  
> Created_at: 2019-09-03 23:24:18 UTC  
> Url: https://github.com/boostorg/gil/pull/386#issuecomment-527677759  

Added description as you suggested.   
Took reference from this link:  
https://caligari.dartmouth.edu/doc/idl/html_6.2/Padding_Images.html

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2019-09-04 08:29:12 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/386#pullrequestreview-283448976  

Thanks for linking the reference, it's an interesting resource.  
  
I just have several requests for minor clean-ups (better to take care of it now than after merge).

📁 include/boost/gil/extension/numeric/algorithm.hpp

```diff
 324 |+ /// \brief adds new row at top and bottom
 325 |+ /** Image padding introduces new pixels around the edges of an image. 
 326 |+  * The border provides space for annotations or acts as a boundary when using advanced filtering techniques.*/
```

> Username: mloskot  
> Created_at: 2019-09-04 08:19:28 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320629897  

Nitpicking: Please, prefer `///` for Doxygen comment blocks

---

📁 include/boost/gil/extension/numeric/algorithm.hpp

```diff
 348 |+ /// \brief adds new column at left and right
 349 |+ /** Image padding introduces new pixels around the edges of an image. 
 350 |+  * The border provides space for annotations or acts as a boundary when using advanced filtering techniques.*/
```

> Username: mloskot  
> Created_at: 2019-09-04 08:19:36 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320629941  

Nitpicking: Please, prefer `///` for Doxygen comment blocks

---

📁 include/boost/gil/extension/numeric/algorithm.hpp

```diff
 277 |+             {
 278 |+                 assign_pixels(
 279 |+                     src_view.row_begin(i - extend_count),
```

> Username: mloskot  
> Created_at: 2019-09-04 08:24:54 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320632312  

All indexes within image view dimensions (`coord_t`, `x_coord_t`, `y_coord_t`, etc.) are (historically) derived in GIL from `std::ptrdiff_t`, not `std::size_t`. So, the use of `std::size_t` may lead to annoying compilation warnings.  
  
Please, ensure this new code does not add up to the already existing warnings flood.

> Username: lpranam  
> Created_at: 2019-09-04 14:26:29 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320790439  

I actually had used `std::ptrdiff_t` diff earlier but it gave me warning due to comparison with `extend_count` which is of type `std::size_t` so looped using `std::size_t` and converted height and width explicitly.   
  
If you insist on using `std::ptrdiff_t` then I will have to convert `extend_count` to `std::ptrdiff_t` from `std::size_t` to avoid warning.

> Username: mloskot  
> Created_at: 2019-09-04 14:50:36 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320804260  

>If you insist on using std::ptrdiff_t then I will have to convert extend_count to std::ptrdiff_t from std::size_t to avoid warning.  
  
I insist on having no warnings. If it means (safe) casting or changing types, your choice.  
If you prefer `size_t extend_count` , then you can cast it internally to `ptrdiff_t`.  
  
In detail, my point is:  
Using common types for indexing pixel rows/columns should be preferred.  
Currently, the common type is derived from `std::ptrdiff_t`. It may change in future - there were discussions where @stefanseefeld suggested better approach or use of `std::size_t` - but _before that happens, any new code should stick to current conventions_.

> Username: lpranam  
> Created_at: 2019-09-04 15:47:42 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320836106  

Finally, I converted `extend_count` to `std::ptrdiff_t` and also checked there are no warning related to it.

> Username: mloskot  
> Created_at: 2019-09-04 15:59:21 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320842227  

@lpranam  Thank you!

---

📁 include/boost/gil/extension/numeric/algorithm.hpp

```diff
 339 |+ 
 340 |+     auto result_view = view(result_img);
 341 |+ 
```

> Username: mloskot  
> Created_at: 2019-09-04 08:26:45 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320633128  

Nitpicking: superfluous blank

---

📁 include/boost/gil/extension/numeric/algorithm.hpp

```diff
 341 |+ 
 342 |+     detail::extend_row_impl(src_view, result_view, extend_count, option);
 343 |+ 
```

> Username: mloskot  
> Created_at: 2019-09-04 08:26:58 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320633236  

Nitpicking: superfluous blank

---

📁 include/boost/gil/extension/numeric/algorithm.hpp

```diff
 365 |+ 
 366 |+     auto result_view = rotated90cw_view(view(result_img));
 367 |+ 
```

> Username: mloskot  
> Created_at: 2019-09-04 08:27:05 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320633283  

Nitpicking: superfluous blank

---

📁 include/boost/gil/extension/numeric/algorithm.hpp

```diff
 384 |+ {
 385 |+     auto auxilary_img = extend_col(src_view, extend_count, option);
 386 |+ 
```

> Username: mloskot  
> Created_at: 2019-09-04 08:27:09 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320633315  

Nitpicking: superfluous blank


---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2019-09-04 15:21:50 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/386#pullrequestreview-283698400  

Looks good to me.  Thank you for your work.  
  
I will leave the compilation warnings issue, if any, for your discretion.

---

## Review 5 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-09-04 19:55:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/386#pullrequestreview-283857700  

📁 include/boost/gil/extension/numeric/algorithm.hpp

```diff
 256 |+ enum class boundary_option
 257 |+ {
 258 |+     boundary_option_extend_zero,    /// assume the source boundaries to be zero
```

> Username: stefanseefeld  
> Created_at: 2019-09-04 19:55:37 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320945858  

There is no need to keep the "boundary_option" prefix in the enumerator name, as the (enum) class already provides the named scope, i.e. users can just write "boundary_option::extend_zero" rather than "boundary_option::boundary_option_extend_zero".

> Username: mloskot  
> Created_at: 2019-09-04 20:28:20 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320959552  

Good point

> Username: lpranam  
> Created_at: 2019-09-04 20:34:33 UTC  
> Updated_at: 2019-09-04 20:48:08 UTC  
> Url: https://github.com/boostorg/gil/pull/386#discussion_r320962347  

That made complete sense. I have updated the code.


---

## Comment 6

> Username: mloskot  
> Created_at: 2019-09-05 16:32:53 UTC  
> Url: https://github.com/boostorg/gil/pull/386#issuecomment-528451366  

@stefanseefeld Are you happy with Pranam's change addressing your https://github.com/boostorg/gil/pull/386#discussion_r320945858 ?  
  
@lpranam Is this ready to merge?

---

## Comment 7

> Username: lpranam  
> Created_at: 2019-09-05 18:34:04 UTC  
> Updated_at: 2019-09-05 18:34:19 UTC  
> Url: https://github.com/boostorg/gil/pull/386#issuecomment-528516723  

> @lpranam Is this ready to merge?  
  
@mloskot from my side it's ready

---

## Comment 8

> Username: mloskot  
> Created_at: 2019-09-05 18:47:35 UTC  
> Url: https://github.com/boostorg/gil/pull/386#issuecomment-528522080  

Thanks!

---
