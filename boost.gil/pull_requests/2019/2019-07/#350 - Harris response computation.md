# #350 Harris response computation [Merged]

> Username: simmplecoder  
> Created at: 2019-07-23 01:15:53 UTC  
> Updated at: 2020-08-01 20:07:33 UTC  
> Merged at: 2019-08-21 03:06:29 UTC  
> Closed at: 2019-08-21 03:06:29 UTC  
> Url: https://github.com/boostorg/gil/pull/350  

### Description  
  
Implement Harris corner detection, but without suggested result filtering. The algorithm writes Harris responses into `gray32_view_t` and the user can apply their chosen filtering/suppression algorithms. Loosening filtering will give edge detector.  
  
### Environment  
  
Everything compatible with Boost 1.70  
  
### References  
  
https://en.wikipedia.org/wiki/Harris_Corner_Detector  
  
https://en.wikipedia.org/wiki/Structure_tensor  
  
https://en.wikipedia.org/wiki/Image_derivatives  
  
http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.434.4816&rep=rep1&type=pdf  
  
### Tasklist  
  
- [X] Implement Harris corner detection algorithm  
- [X] Extract contents of current test file into demo  
- [X] Add test cases (simplistic for now)  
- [X] <s> Write docs that would be readable for most people</s> Will be covered by another PR.  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-07-23 06:44:45 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/350#pullrequestreview-265192481  

@simmplecoder I've taken the liberty of requesting a few clarifications and cleanups

📁 include/boost/gil/image_processing/harris.hpp

```diff
  41 |+     auto half_length = window_length / 2;
  42 |+ 
  43 |+     for (auto y = half_length; y < height - half_length; ++y) {
```

> Username: mloskot  
> Created_at: 2019-07-23 06:25:25 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306145745  

Please, open curly brackets/blocks in new lines, here and below

---

📁 include/boost/gil/image_processing/harris.hpp

```diff
  38 |+     auto window_length = weights.dimensions().x;
  39 |+     auto width = m11.width();
  40 |+     auto height = m11.height();
```

> Username: mloskot  
> Created_at: 2019-07-23 06:28:13 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306146546  

Should read `auto const width =...`, for all immutable variables. This allows a reader of this code to reason with one or two questions less about the code.

---

📁 include/boost/gil/image_processing/harris.hpp

```diff
   1 |+ #ifndef BOOST_GIL_HARRIS_HPP
```

> Username: mloskot  
> Created_at: 2019-07-23 06:29:24 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306146850  

`_IMAGE_PROCESSING_`

---

📁 include/boost/gil/image_processing/harris.hpp

```diff
  25 |+ void compute_harris_responses(
  26 |+     gil::gray32f_view_t m11,
  27 |+     gil::gray32f_view_t m12_21,
```

> Username: mloskot  
> Created_at: 2019-07-23 06:33:07 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306147787  

This namespace qualification is confusing a bit. It's referring to current scope namespace already, no?  
I think it's not necessary.

> Username: simmplecoder  
> Created_at: 2019-07-25 10:15:59 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r307221400  

I made qualification consistent, now both have full qualification. I'll add the question as issue, so we could review this after GSoC is over and include into file cleanup that you mentioned.

> Username: mloskot  
> Created_at: 2019-07-25 11:47:37 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r307254255  

Looks good, thanks for follow-up in #352


📁 include/boost/gil/image_processing/numeric.hpp

```diff
  53 |+ inline void compute_tensor_entries(
  54 |+     boost::gil::gray16_view_t dx,
  55 |+     boost::gil::gray16_view_t dy,
```

> Username: mloskot  
> Created_at: 2019-07-23 06:31:09 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306147293  

Why namespace qualification here but earlier not? I don't think this is nessecary.

> Username: mloskot  
> Created_at: 2019-07-25 11:48:11 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r307254459  

This will be addressed as part of #352


📁 example/harris.cpp

```diff
  12 |+ namespace gil = boost::gil;
  13 |+ 
  14 |+ gil::gray8_image_t to_grayscale(gil::rgb8_view_t original) {
```

> Username: mloskot  
> Created_at: 2019-07-23 06:36:17 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306148597  

Please add a comment why the default color conversion is not suitable here.  
Otherwise it raises obvious question.  
(Less questions raised, higher code quality.)

> Username: stefanseefeld  
> Created_at: 2019-07-24 03:18:25 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306613175  

(formatting nit-pick: and please put curly brackets onto their own lines.)

---

📁 example/harris.cpp

```diff
  66 |+             for (size_t filterY = 0; filterY < filterHeight; ++filterY) {
  67 |+                 for (size_t filterX = 0; filterX < filterWidth; ++filterX) {
  68 |+                     int imageX = x - filterWidth / 2 + filterX;
```

> Username: mloskot  
> Created_at: 2019-07-23 06:37:22 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306148908  

Please, don't use any variants of CamelCase for naming variables.

---

📁 example/harris.cpp

```diff
  72 |+                         continue;
  73 |+                     }
  74 |+                     auto& pixel = input_view(gil::point_t(imageX, imageY));
```

> Username: mloskot  
> Created_at: 2019-07-23 06:39:12 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306149438  

I think `point_t` construction is not necessary. There is x, y overload.

---

📁 example/harris.cpp

```diff
  78 |+             }
  79 |+             auto& pixel = output_view(gil::point_t(x, y));
  80 |+             pixel = std::min(std::max(int(factor * intensity + bias), 0), 255);
```

> Username: mloskot  
> Created_at: 2019-07-23 06:42:52 UTC  
> Updated_at: 2019-08-20 16:03:27 UTC  
> Url: https://github.com/boostorg/gil/pull/350#discussion_r306150462  

We may need to accommodate here for this annoyance on MSVC, see #328


---

## Comment 2

> Username: simmplecoder  
> Created_at: 2019-08-09 17:47:04 UTC  
> Updated_at: 2019-08-09 17:52:36 UTC  
> Url: https://github.com/boostorg/gil/pull/350#issuecomment-520006375  

@stefanseefeld , if you don't have any more suggestions, could you please approve the PR? Last commit introduced license and copyright notices. Design PR will come as a different PR.

---
