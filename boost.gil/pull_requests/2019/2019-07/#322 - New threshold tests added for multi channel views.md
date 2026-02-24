# #322 New threshold tests added for multi channel views [Merged]

> Username: miralshah365  
> Created at: 2019-07-05 13:22:22 UTC  
> Updated at: 2020-02-22 13:10:48 UTC  
> Merged at: 2019-07-17 20:02:30 UTC  
> Closed at: 2019-07-17 20:02:30 UTC  
> Url: https://github.com/boostorg/gil/pull/322  

### Description  
  
*New tests added for threshold_binary and threshold_truncate for RGB image to provide better tests coverage for multichannel views*  
  
  
### Tasklist  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-07-05 20:21:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/322#pullrequestreview-258538547  

📁 test/core/image_processing/threshold_truncate.cpp

```diff
  25 |+     /*                Gray to Gray                 */
  26 |+     /*                                             */
  27 |+     /*---------------------------------------------*/
```

> Username: mloskot  
> Created_at: 2019-07-05 20:13:10 UTC  
> Updated_at: 2019-07-10 23:47:21 UTC  
> Url: https://github.com/boostorg/gil/pull/322#discussion_r300787841  

The comment separators are not the best choice to structure the code.   
Simply, extract each test case as a regular function and call from the `main`, like so:  
  
https://github.com/boostorg/gil/blob/4ed7701b47764edf327e7c82f65cf9815026bdc2/test/core/image_view/collection.cpp#L93-L97  
  
For example:  
  
```  
binary_gray_to_gray  
binary_inverse_gray_to_gray  
truncate_threshold_regular_gray_to_gray  
truncate_zero_regular_gray_to_gray  
```  
etc.  
  
At one point, especially after making I/O PNM a built-in format, https://lists.boost.org/boost-gil/2019/07/0255.php, we may decide to reformat the tests from the Boost.LightweightTest to regular Boost.Test.  
  
Diving into Boost.Test may be too distracting for you, so I don't expect you do be doing that.


📁 test/core/image_processing/threshold_binary.cpp

```diff
  12 | #include <boost/core/lightweight_test.hpp>
  13 | 
  14 |+ #include <boost/gil/extension/io/jpeg.hpp>
```

> Username: mloskot  
> Created_at: 2019-07-05 20:19:38 UTC  
> Updated_at: 2019-07-10 23:47:21 UTC  
> Url: https://github.com/boostorg/gil/pull/322#discussion_r300788902  

This seems unused.


---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2019-07-14 20:49:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/322#pullrequestreview-261598637  

@miralshah365 LGTM! Please, feel free to merge.

---
