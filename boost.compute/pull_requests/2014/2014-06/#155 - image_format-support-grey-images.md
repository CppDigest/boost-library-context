# #155 image_format-support-grey-images [Merged]

> Username: Mageswaran1989  
> Created at: 2014-06-21 11:20:50 UTC  
> Updated at: 2014-06-25 01:42:03 UTC  
> Merged at: 2014-06-25 01:39:52 UTC  
> Closed at: 2014-06-25 01:39:52 UTC  
> Url: https://github.com/boostorg/compute/pull/155  

OpenCV Grey Image  Support  
Hi,  
Please check   
https://groups.google.com/forum/?utm_medium=email&utm_source=footer#!topic/boost-compute/UhyFZin1wsw

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-06-21 11:51:25 UTC  
> Url: https://github.com/boostorg/compute/pull/155#issuecomment-46751808  

[![Coverage Status](https://coveralls.io/builds/890327/badge)](https://coveralls.io/builds/890327)  
  
Coverage remained the same when pulling **81994ed6c9d2a83efa9227affc1ac68149caf724 on Mageswaran1989:image_format-support-grey-images** into **1d5a088e9414ddb6370e06ae9aa6c805a3ec9bc5 on kylelutz:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-06-21 11:57:38 UTC  
> Url: https://github.com/boostorg/compute/pull/155#issuecomment-46751904  

[![Coverage Status](https://coveralls.io/builds/890356/badge)](https://coveralls.io/builds/890356)  
  
Coverage remained the same when pulling **81994ed6c9d2a83efa9227affc1ac68149caf724 on Mageswaran1989:image_format-support-grey-images** into **1d5a088e9414ddb6370e06ae9aa6c805a3ec9bc5 on kylelutz:develop**.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-06-21 16:28:41 UTC  
> Updated_at: 2014-06-23 02:08:36 UTC  
> Url: https://github.com/boostorg/compute/pull/155#issuecomment-46758082  

Left one comment inline. Can you try this out with `CL_INTENSITY`?

---

## Comment 4

> Username: coveralls  
> Created_at: 2014-06-24 16:08:47 UTC  
> Url: https://github.com/boostorg/compute/pull/155#issuecomment-46992221  

[![Coverage Status](https://coveralls.io/builds/899012/badge)](https://coveralls.io/builds/899012)  
  
Coverage remained the same when pulling **f10d3d549d1f5b3254d4e90f44cd20ca7302a406 on Mageswaran1989:image_format-support-grey-images** into **1d5a088e9414ddb6370e06ae9aa6c805a3ec9bc5 on kylelutz:develop**.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-06-25 01:42:03 UTC  
> Url: https://github.com/boostorg/compute/pull/155#issuecomment-47051766  

Looks good! Merged!  
  
It would be good to have some more testing for the OpenCV interop code. There is a little already in the `test/test_interop_opencv.cpp` file but it is very basic. Probably more testing for the different `cv::Mat` types and copying them back and forth between OpenCL `image2d` objects.

---
