# #577 Fix convolve_2d for images with float32_t channel model [Merged]

> Username: harshitpant1  
> Created at: 2021-03-15 13:42:13 UTC  
> Updated at: 2022-02-09 13:43:49 UTC  
> Merged at: 2022-02-09 13:43:49 UTC  
> Closed at: 2022-02-09 13:43:49 UTC  
> Url: https://github.com/boostorg/gil/pull/577  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Convolve_2d currently does not work  with images using float32_t channel model due to a trivial issue. This PR fixes that.  
  
### References  
  
Fixes #575  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: harshitpant1  
> Created_at: 2021-03-15 14:02:53 UTC  
> Url: https://github.com/boostorg/gil/pull/577#issuecomment-799445201  

Fixing this would help me submit a PR for https://github.com/harsh-4/gil/commit/05ec89edbbf8bacb971e2d3ec5c4fa5cc48d5d3a

---

## Comment 2

> Username: lpranam  
> Created_at: 2021-03-15 14:06:02 UTC  
> Url: https://github.com/boostorg/gil/pull/577#issuecomment-799447586  

will try to have a look as soon as possible then...

---

## Comment 3

> Username: harshitpant1  
> Created_at: 2021-03-15 14:09:52 UTC  
> Url: https://github.com/boostorg/gil/pull/577#issuecomment-799450421  

Thanks.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2021-03-15 14:41:00 UTC  
> Updated_at: 2021-04-03 01:06:40 UTC  
> Url: https://github.com/boostorg/gil/pull/577#issuecomment-799474063  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/577?src=pr&el=h1) Report  
> Merging [#577](https://codecov.io/gh/boostorg/gil/pull/577?src=pr&el=desc) (b16d5a9) into [develop](https://codecov.io/gh/boostorg/gil/commit/6da59cc3351e5657275d3a536e0b6e7a1b6ac738?el=desc) (6da59cc) will **increase** coverage by `0.13%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #577      +/-   ##  
===========================================  
+ Coverage    78.58%   78.72%   +0.13%       
===========================================  
  Files          117      118       +1       
  Lines         5002     5034      +32       
===========================================  
+ Hits          3931     3963      +32       
  Misses        1071     1071                
```

---

## Review 5 [Commented]

> Username: mloskot  
> Created_at: 2021-04-02 22:48:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/577#pullrequestreview-627397605  

📁 test/extension/numeric/convolve_2d.cpp

```diff
  91 |+ gil::gray32f_image_t img_gray_out(9, 9);
  92 |+ gil::gray32fc_view_t src_view =
  93 |+         gil::interleaved_view(9, 9, reinterpret_cast<const gil::gray32f_pixel_t*>(img), 9);
```

> Username: mloskot  
> Created_at: 2021-04-02 22:48:19 UTC  
> Updated_at: 2021-04-02 22:49:26 UTC  
> Url: https://github.com/boostorg/gil/pull/577#discussion_r606473247  

Nitpick: eastconst `gil::gray32f_pixel_t const*`


---

## Review 6 [Commented]

> Username: mloskot  
> Created_at: 2021-04-02 22:48:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/577#pullrequestreview-627397669  

📁 test/extension/numeric/convolve_2d.cpp

```diff
  95 |+         gil::interleaved_view(9, 9, reinterpret_cast<const gil::gray32f_pixel_t*>(exp_output), 9);
  96 |+ std::vector<float> v(1, 1);
  97 |+ gil::detail::kernel_2d<float> kernel(v.begin(), v.size(), 0, 0); //impulse kernel
```

> Username: mloskot  
> Created_at: 2021-04-02 22:48:38 UTC  
> Updated_at: 2021-04-02 22:49:26 UTC  
> Url: https://github.com/boostorg/gil/pull/577#discussion_r606473452  

Nitpick: space after `//`


---

## Review 7 [Approved]

> Username: mloskot  
> Created_at: 2021-04-02 22:50:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/577#pullrequestreview-627397988  

Looks good to me

---

## Comment 8

> Username: harshitpant1  
> Created_at: 2021-04-03 03:13:01 UTC  
> Url: https://github.com/boostorg/gil/pull/577#issuecomment-812801669  

Thanks for looking into this, will remember your suggestions moving forward.

---

## Review 9 [Commented]

> Username: sdebionne  
> Created_at: 2021-10-05 07:40:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/577#pullrequestreview-771083695  

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 375 |                         aux_total +=
 376 |-                             src_view(col_boundary, row_boundary) *
 376 |+                             src_view(col_boundary, row_boundary)[0] *
```

> Username: sdebionne  
> Created_at: 2021-10-05 07:40:32 UTC  
> Url: https://github.com/boostorg/gil/pull/577#discussion_r721970907  

Convolution algorithms only work on grayscale images? I am wondering what this extra [0] means... get the first channel?

> Username: meshtag  
> Created_at: 2021-10-05 07:46:57 UTC  
> Url: https://github.com/boostorg/gil/pull/577#discussion_r721975841  

> I am wondering what this extra [0] means... get the first channel?  
  
We are trying to access the pixel value of a specific channel from a channel specific view. [This](https://github.com/boostorg/gil/blob/c9b339e5d0b6a1d121ff9b37ae2d7544c534dc68/include/boost/gil/extension/numeric/convolve.hpp#L435) and [this](https://github.com/boostorg/gil/blob/c9b339e5d0b6a1d121ff9b37ae2d7544c534dc68/include/boost/gil/rasterization/ellipse.hpp#L137) may provide more context.


---

## Comment 10

> Username: meshtag  
> Created_at: 2022-02-09 13:41:15 UTC  
> Url: https://github.com/boostorg/gil/pull/577#issuecomment-1033773271  

@mloskot, do you think this can land in the `dev` branch?  
Merging this would also help us move ahead with other associated PRs.

---

## Review 11 [Approved]

> Username: mloskot  
> Created_at: 2022-02-09 13:42:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/577#pullrequestreview-877477063  

I'm fine to merge this.

---
