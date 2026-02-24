# #364 Hessian corner detector [Merged]

> Username: simmplecoder  
> Created at: 2019-08-03 17:49:12 UTC  
> Updated at: 2020-08-01 20:07:41 UTC  
> Merged at: 2019-08-21 06:37:54 UTC  
> Closed at: 2019-08-21 06:37:54 UTC  
> Url: https://github.com/boostorg/gil/pull/364  

### Description  
  
This commit implements Hessian corner detector, with response function being the same as in [Harris](https://github.com/boostorg/gil/pull/350).   
  
### Compatibility  
  
Anything compatible with Boost 1.70  
  
### References  
  
https://en.wikipedia.org/wiki/Hessian_matrix  
  
### Tasklist  
  
- [X] Implement Hessian corner detection algorithm  
- [X] Add inline docs  
- [X] <s>Add larger docs to explain the algorithm better</s> will be covered by another PR  
- [X] Add test cases  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-08-09 19:39:29 UTC  
> Url: https://github.com/boostorg/gil/pull/364#issuecomment-520039754  

@simmplecoder I know close to nothing about Hessian detector. I can only offer a nitpicking C++ guidelines/style review. Do you still wish to have one?

---

## Comment 2

> Username: simmplecoder  
> Created_at: 2019-08-10 11:09:00 UTC  
> Url: https://github.com/boostorg/gil/pull/364#issuecomment-520140370  

@mloskot , yes, any constructive criticism is welcome! (Boost guidelines, GIL guidelines, any other concerns).

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-10 21:03:55 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/364#pullrequestreview-273433649  

📁 example/hessian.cpp

```diff
   1 |+ #include <boost/gil/image.hpp>
   2 |+ #include <boost/gil/image_view.hpp>
   3 |+ #include <boost/gil/extension/io/png.hpp>
```

> Username: mloskot  
> Created_at: 2019-08-10 17:38:03 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312710373  

A minor issue: `/extension/` should go either at the top or at the bottom of `<boost/gil/...` section.  
Here is suggestion how to order https://github.com/boostorg/gil/wiki/Include-Directives-Order

---

📁 example/hessian.cpp

```diff
  22 |+     auto output = gil::view(output_image);
  23 |+     constexpr double max_channel_intensity = (std::numeric_limits<std::uint8_t>::max)();
  24 |+     for (long int y = 0; y < original.height(); ++y) {
```

> Username: mloskot  
> Created_at: 2019-08-10 17:38:40 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312710394  

```  
for (...)  
{  
```

---

📁 example/hessian.cpp

```diff
  29 |+             double green_intensity = original(x, y).at(std::integral_constant<int, 1>{})
  30 |+                 / max_channel_intensity;
  31 |+             double blue_intensity = original(x, y).at(std::integral_constant<int, 2>{})
```

> Username: mloskot  
> Created_at: 2019-08-10 20:51:40 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312714996  

Replace three requests for reference to pixel with one  
```  
auto const& p = original(x, u);  
double red_intensity = p.at(std::integral_constant<int, 0>{});  
...  
```

---

📁 example/hessian.cpp

```diff
  37 |+             // perform gamma adjustment
  38 |+             double gamma_compressed_luminosity = 0;
  39 |+             if (linear_luminosity < 0.0031308) {
```

> Username: mloskot  
> Created_at: 2019-08-10 20:52:17 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715025  

```  
if (...)  
{  
```

---

📁 example/hessian.cpp

```diff
  71 |+         for (long y = 0; y < height; ++y) {
  72 |+             double intensity = 0.0;
  73 |+             for (size_t filter_y = 0; filter_y < filterHeight; ++filter_y) {
```

> Username: mloskot  
> Created_at: 2019-08-10 20:53:33 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715050  

`filterHeight` - please don't use any form of camelCase-ing

---

📁 example/hessian.cpp

```diff
  68 |+     const auto height = input_view.height();
  69 |+     const auto width = input_view.width();
  70 |+     for (long x = 0; x < width; ++x) {
```

> Username: mloskot  
> Created_at: 2019-08-10 20:54:07 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715067  

Could you write a comment explaining why this can not use any of the `convolve` methods?

> Username: simmplecoder  
> Created_at: 2019-08-11 21:20:02 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312754722  

There is none, unfortunately. I just kept copy pasting for rapid prototyping, but yes, the example code might need a better cleanup

---

📁 example/hessian.cpp

```diff
  73 |+             for (size_t filter_y = 0; filter_y < filterHeight; ++filter_y) {
  74 |+                 for (size_t filter_x = 0; filter_x < filterWidth; ++filter_x) {
  75 |+                     int image_x = x - filterWidth / 2 + filter_x;
```

> Username: mloskot  
> Created_at: 2019-08-10 20:55:03 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715090  

Replace `int` with `coord_x`?  
A `double` to integral cast is necessary, isn't it?

> Username: simmplecoder  
> Created_at: 2019-08-11 21:17:07 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312754649  

Not sure what you mean by the second part. Could you please clarify?

---

📁 example/hessian.cpp

```diff
  74 |+                 for (size_t filter_x = 0; filter_x < filterWidth; ++filter_x) {
  75 |+                     int image_x = x - filterWidth / 2 + filter_x;
  76 |+                     int image_y = y - filterHeight / 2 + filter_y;
```

> Username: mloskot  
> Created_at: 2019-08-10 20:55:51 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715101  

Replace `int` with `coord_y`?

---

📁 example/hessian.cpp

```diff
  79 |+                         continue;
  80 |+                     }
  81 |+                     auto& pixel = input_view(image_x, image_y);
```

> Username: mloskot  
> Created_at: 2019-08-10 20:56:36 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715116  

Is the pixel being modified?  
If not, please indicate it's read-only using `auto const&`

---

📁 example/hessian.cpp

```diff
 153 |+     double harris_response_threshold)
 154 |+ {
 155 |+     std::vector<gil::point_t> corner_points;
```

> Username: mloskot  
> Created_at: 2019-08-10 20:59:24 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715174  

If size upper limit is known, It may be sensible to `corner_points.reserve` it.

> Username: simmplecoder  
> Created_at: 2019-08-11 21:17:30 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312754655  

Unfortunately no, resolving this one


📁 include/boost/gil/image_processing/hessian.hpp

```diff
  16 |+ template <typename GradientView, typename Weights, typename OutputView>
  17 |+ inline void compute_hessian_responses(
  18 |+     GradientView ddxx,
```

> Username: mloskot  
> Created_at: 2019-08-10 21:00:48 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715200  

`ddxx`, `dxdy` and `ddyy` are pretty cryptic. Please, either consider more descriptive names or `/// \param ...` describe them.

> Username: simmplecoder  
> Created_at: 2019-08-11 21:18:17 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312754679  

I adjusted docs to include an example of the notation, hopefully it will be more clear now. Could you please resolve this and the one below if it gets better?


📁 include/boost/gil/image_processing/numeric.hpp

```diff
  61 |+     GradientView dx,
  62 |+     GradientView dy,
  63 |+     OutputView ddxx,
```

> Username: mloskot  
> Created_at: 2019-08-10 21:01:34 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715216  

Again, the names deserve a comment with description.

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
  94 |+         {
  95 |+             pixel_t ddxx_i;
  96 |+             boost::gil::static_transform(ddxx_i, ddxx_i,
```

> Username: mloskot  
> Created_at: 2019-08-10 21:01:56 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715226  

`boost::gil::` is not necessary


📁 test/core/image_processing/hessian.cpp

```diff
  14 |+ namespace gil = boost::gil;
  15 |+ 
  16 |+ bool are_equal(gil::gray32f_view_t expected, gil::gray32f_view_t actual) {
```

> Username: mloskot  
> Created_at: 2019-08-10 21:02:35 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r312715237  

Here and everywhere   
```  
... (...)  
{  
```


---

## Review 4 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-08-20 02:49:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/364#pullrequestreview-276907286  

📁 example/hessian.cpp

```diff
  93 |+             }
  94 |+             auto& pixel = output_view(gil::point_t(x, y));
  95 |+             pixel = (std::min)((std::max)(int(factor * intensity + bias), 0), 255);
```

> Username: stefanseefeld  
> Created_at: 2019-08-20 02:49:24 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315487517  

Can you explain the `(std::min)` construct ? Why the parentheses ?

> Username: simmplecoder  
> Created_at: 2019-08-20 02:54:10 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315488370  

@stefanseefeld a user came in asking about handling of max/min function macros of Windows in GIL. The outcome was that GIL already accommodated for that, and I followed the suit.

> Username: stefanseefeld  
> Created_at: 2019-08-20 02:55:59 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315488646  

I don't understand. If `max` is defined as a macro, `std::max` itself would already evaluate to an invalid expression, wouldn't it ? How do the parentheses help ?

> Username: simmplecoder  
> Created_at: 2019-08-20 03:02:32 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315489581  

`std::max` will be syntax error, but with parentheses will prevent macro expansion, here is an example: https://godbolt.org/z/kotbUp

> Username: stefanseefeld  
> Created_at: 2019-08-20 03:07:22 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315490372  

ok, thanks.

> Username: simmplecoder  
> Created_at: 2019-08-20 03:12:17 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315491199  

From my understanding, `(x)` only works for entities, and since a macro is not C++ entity, compiler understands that we want to force C++ version of `std::max` text, then the entity gets it's `()` operator invoked.

> Username: mloskot  
> Created_at: 2019-08-20 08:16:30 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315560590  

@stefanseefeld Unfortunately, `std::max` does noy prevent macros expansion. The extra parenthesis are required. They're also required according to Boost guidelines and inspection tool, see https://github.com/boostorg/gil/issues/376


---

## Review 5 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-08-20 02:52:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/364#pullrequestreview-276907862  

📁 example/hessian.cpp

```diff
  83 |+                     int image_y = y - filter_height / 2 + filter_y;
  84 |+                     if (image_x >= input_view.width() || image_x < 0
  85 |+                         || image_y >= input_view.height() || image_y < 0)
```

> Username: stefanseefeld  
> Created_at: 2019-08-20 02:52:01 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315487982  

Minor formatting-nit-pick: I'd put the binary operator (`||`) at the and of the previous line, which aligns the two sub-expressions.


---

## Review 6 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-08-20 02:52:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/364#pullrequestreview-276908034  

📁 example/hessian.cpp

```diff
  74 |+     const auto width = input_view.width();
  75 |+     for (std::ptrdiff_t x = 0; x < width; ++x) {
  76 |+         for (std::ptrdiff_t y = 0; y < height; ++y) {
```

> Username: stefanseefeld  
> Created_at: 2019-08-20 02:52:49 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315488132  

minor nit-pick: please put the curly brackets onto their own lines as you do elsewhere.


---

## Review 7 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-08-20 02:54:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/364#pullrequestreview-276908273  

📁 example/hessian.cpp

```diff
  34 |+             double blue_intensity = p.at(std::integral_constant<int, 2>{})
  35 |+                 / max_channel_intensity;
  36 |+             auto linear_luminosity = 0.2126 * red_intensity
```

> Username: stefanseefeld  
> Created_at: 2019-08-20 02:54:05 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315488353  

It would be useful to provide a literature reference to explain the choice of those three weighing factors in the luminosity computation.

> Username: simmplecoder  
> Created_at: 2019-08-20 03:10:15 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315490893  

I have added a link to formula used at the top of the function, is it sufficient?

> Username: stefanseefeld  
> Created_at: 2019-08-20 03:12:47 UTC  
> Updated_at: 2019-08-21 03:22:13 UTC  
> Url: https://github.com/boostorg/gil/pull/364#discussion_r315491273  

Sure, thanks.


---
