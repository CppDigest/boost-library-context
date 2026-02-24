# #143 added opencv_filter based on convolution: renamed opencv_filter to opencv_flip [Closed]

> Username: Mageswaran1989  
> Created at: 2014-06-18 13:07:49 UTC  
> Updated at: 2014-06-20 12:06:48 UTC  
> Closed at: 2014-06-20 06:59:59 UTC  
> Url: https://github.com/boostorg/compute/pull/143  

Please  review the pull.

---

## Comment 1

> Username: Mageswaran1989  
> Created_at: 2014-06-18 13:43:44 UTC  
> Url: https://github.com/boostorg/compute/pull/143#issuecomment-46436745  

CL convolution kernel is a primitive one!

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-06-18 13:49:11 UTC  
> Url: https://github.com/boostorg/compute/pull/143#issuecomment-46437434  

[![Coverage Status](https://coveralls.io/builds/879880/badge)](https://coveralls.io/builds/879880)  
  
Coverage remained the same when pulling **98a1731800bbc6e7fc60ec47da3aa8c43d5e18d4 on Mageswaran1989:opencv-convolution-filter-example** into **5f406314aec225e1598b0a6a3dab12b67c70c179 on kylelutz:master**.

---

## Comment 3

> Username: coveralls  
> Created_at: 2014-06-18 13:53:10 UTC  
> Url: https://github.com/boostorg/compute/pull/143#issuecomment-46437937  

[![Coverage Status](https://coveralls.io/builds/879909/badge)](https://coveralls.io/builds/879909)  
  
Coverage increased (+3.47%) when pulling **98a1731800bbc6e7fc60ec47da3aa8c43d5e18d4 on Mageswaran1989:opencv-convolution-filter-example** into **5f406314aec225e1598b0a6a3dab12b67c70c179 on kylelutz:master**.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-06-19 04:55:41 UTC  
> Url: https://github.com/boostorg/compute/pull/143#issuecomment-46523593  

Would you mind splitting this into two pull-requests? One for renaming the current example to `opencv_flip` and another to add the new `opencv_filter` example (or should we call it `opencl_convolution`?). Smaller pull-request which just add/change one thing are easier to review and merge. Thanks!

---
