# #393 Implemented median_filter [Merged]

> Username: miralshah365  
> Created at: 2019-09-25 15:59:36 UTC  
> Updated at: 2020-02-22 13:07:41 UTC  
> Merged at: 2019-10-15 04:42:55 UTC  
> Closed at: 2019-10-15 04:42:55 UTC  
> Url: https://github.com/boostorg/gil/pull/393  

### Description  
  
implemented `median_filter` function  
  
tests added for `median_filter`  
  
This utility will provide a way to remove noise from the image by choosing a median value from a window which iterates over entire image.  
  
### Tasklist  
  
- [X] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2019-10-01 20:01:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/393#pullrequestreview-295857447  

📁 test/core/image_processing/median_filter.cpp

```diff
  13 |+ #include <boost/gil/algorithm.hpp>
  14 |+ #include <boost/gil/gray.hpp>
  15 |+ #include <boost/gil/image_processing/filter.hpp>
```

> Username: mloskot  
> Created_at: 2019-10-01 20:01:25 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r330247985  

@miralshah365 Regarding the CI builds failure, I wonder if replacing these four includes with single `#include <boost/gil.hpp>` would solve the issue. Possibly, some headers may not be self-contained enough.

> Username: miralshah365  
> Created_at: 2019-10-07 15:12:47 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r332076777  

After making suggested changes I still get the same errors.


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2019-10-07 16:04:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/393#pullrequestreview-298240369  

📁 include/boost/gil/image_processing/filter.hpp

```diff
  96 |+             static_transform(
  97 |+                 subimage_view(extended_view, x - half_kernel_size, y - half_kernel_size, kernel_size, kernel_size),
  98 |+                 dst_it[x],
```

> Username: mloskot  
> Created_at: 2019-10-07 16:03:59 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r332104768  

@miralshah365 First, `static_transform` accepts and operates on color base - [a container of color elements](https://www.boost.org/doc/libs/develop/libs/gil/doc/html/design/color_base.html) e.g. pixels, but not on image views.  
  
Second, above you are mixing both, view as source with pixel as destination.  
  
Since `layout_t` is a trait of color base (e.g. pixel) and not image view, then you are getting this particular compilation error:  
  
```  
gil/color_base_algorithm.hpp:106:78: error: no type named ‘layout_t’ in ‘class boost::gil::image_view<....>  
```  
  
Does it make sense and help?

> Username: miralshah365  
> Created_at: 2019-10-11 00:57:30 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r333792151  

Yes, this was the problem. Correcting that solved all the issues with my code

> Username: mloskot  
> Created_at: 2019-10-11 07:54:34 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r333869091  

All the CI-s are happy now. Good job!   
  
I will review it over the weekend.


---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2019-10-13 15:44:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/393#pullrequestreview-301053751  

📁 test/core/image_processing/median_filter.cpp

```diff
  12 |+ #include <boost/gil/image_view.hpp>
  13 |+ #include <boost/gil/algorithm.hpp>
  14 |+ #include <boost/gil/gray.hpp>
```

> Username: mloskot  
> Created_at: 2019-10-13 15:44:06 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r334283228  

Nitpicking for future:  these should be kept in order https://github.com/boostorg/gil/wiki/Include-Directives-Order


---

## Review 4 [Commented]

> Username: mloskot  
> Created_at: 2019-10-13 15:45:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/393#pullrequestreview-301053815  

📁 include/boost/gil/image_processing/filter.hpp

```diff
  74 |+     DstView const& dst_view,
  75 |+     std::size_t kernel_size
  76 |+ )
```

> Username: mloskot  
> Created_at: 2019-10-13 15:45:51 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r334283292  

Nitpicking for future: this breaking is unnecessary as it could fit our 100 characters line length limit  
  
```cpp  
void impl_median(SrcView const& src_view, DstView const& dst_view, std::size_t kernel_size)  
```


---

## Review 5 [Commented]

> Username: mloskot  
> Created_at: 2019-10-13 15:46:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/393#pullrequestreview-301053833  

📁 include/boost/gil/image_processing/filter.hpp

```diff
  78 |+     std::size_t half_kernel_size = kernel_size / 2;
  79 |+ 
  80 |+     //deciding output channel type and creating functor
```

> Username: mloskot  
> Created_at: 2019-10-13 15:46:21 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r334283306  

Nitpicking for future:   
```  
 //deciding  
```  
should read  
```  
 // deciding  
```


---

## Review 6 [Changes requested]

> Username: mloskot  
> Created_at: 2019-10-13 16:29:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/393#pullrequestreview-301055420  

@miralshah365 Good work.  
  
The only important comment I have is about the optimisation of `std::vector` use.  
Once we resolve that one, your PR is ready to merge.

📁 include/boost/gil/image_processing/filter.hpp

```diff
  93 |+                 kernel_size
  94 |+             );
  95 |+             std::vector<src_channel_t> values(sub_view.begin(), sub_view.end());
```

> Username: mloskot  
> Created_at: 2019-10-13 16:27:13 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r334285008  

I think this `vector` is likely/potentially reallocated many times but unnecessarily since the size upper limit is known and constant.  
  
Do you agree with slight optimisation like this?  
  
```  
diff --git a/include/boost/gil/image_processing/filter.hpp b/include/boost/gil/image_processing/filter.hpp  
index 32f4e6c4a..1ba6b2f38 100644  
--- a/include/boost/gil/image_processing/filter.hpp  
+++ b/include/boost/gil/image_processing/filter.hpp  
@@ -80,6 +80,8 @@ void impl_median(SrcView const& src_view, DstView const& dst_view, std::size_t k  
     // deciding output channel type and creating functor  
     using src_channel_t = typename channel_type<SrcView>::type;  
  
+    std::vector<src_channel_t> values;  
+    values.reserve(kernel_size * kernel_size);  
     for (std::ptrdiff_t y = 0; y < src_view.height(); y++)  
     {  
         typename DstView::x_iterator dst_it = dst_view.row_begin(y);  
@@ -92,7 +94,7 @@ void impl_median(SrcView const& src_view, DstView const& dst_view, std::size_t k  
                 kernel_size,  
                 kernel_size  
             );  
-            std::vector<src_channel_t> values(sub_view.begin(), sub_view.end());  
+            values.assign(sub_view.begin(), sub_view.end());  
  
             std::nth_element(values.begin(), values.begin() + (values.size() / 2), values.end());  
             dst_it[x] = values[values.size() / 2];  
```  
  
The `values.assign` will compare current capacity with new size required, and will not re-allocate unless it is necessary (`newsize > capacity`).

> Username: miralshah365  
> Created_at: 2019-10-13 17:30:51 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r334287480  

I was thinking something similar but creating a vector in the loop didn't feel right. Thank you for pointing this. I corrected it.

---

📁 include/boost/gil/image_processing/filter.hpp

```diff
  74 |+ {
  75 |+ template <typename SrcView, typename DstView>
  76 |+ void impl_median(SrcView const& src_view, DstView const& dst_view, std::size_t kernel_size)
```

> Username: mloskot  
> Created_at: 2019-10-13 16:27:58 UTC  
> Updated_at: 2019-10-13 17:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/393#discussion_r334285041  

Please, rename `impl_median` to `median_impl` or even `filter_median_impl`.  
  
The `_impl` should always be  a postfix, not prefix.


---

## Review 7 [Approved]

> Username: mloskot  
> Created_at: 2019-10-13 18:10:02 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/393#pullrequestreview-301059501  

@miralshah365 Thank you for your prompt updates. It looks good. Feel free to merge as soon as all our CI-s pass.

---
