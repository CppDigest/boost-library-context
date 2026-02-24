# #657 check result of flush and finish [Merged]

> Username: Slonegg  
> Created at: 2016-09-14 19:09:08 UTC  
> Updated at: 2016-10-19 04:08:47 UTC  
> Merged at: 2016-10-19 04:08:42 UTC  
> Closed at: 2016-10-19 04:08:42 UTC  
> Url: https://github.com/boostorg/compute/pull/657  

At least on NVIDIA most of the OpenCL commands like clEnqueueNDRangeKernel won't return an error if it happened inside kernel. Instead failure will be reported when command queue is flushed. So it is convenient to check errors in kernel by adding clFinish call after enqueue.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-09-14 20:52:18 UTC  
> Url: https://github.com/boostorg/compute/pull/657#issuecomment-247149426  

[![Coverage Status](https://coveralls.io/builds/7889800/badge)](https://coveralls.io/builds/7889800)  
  
Coverage decreased (-0.009%) to 83.329% when pulling **9b2e9d05ab51ab5d15429a1fdb8e3e29526cd7b4 on Slonegg:check_flush_and_finish** into **86211062a771ab0b494116b4223b63a292ad7d11 on boostorg:master**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-09-24 15:54:01 UTC  
> Url: https://github.com/boostorg/compute/pull/657#issuecomment-249371869  

Thanks for the patch! Will get this merged in after the upcoming 1.62 release (should be in the next week or so).

---

## Comment 3

> Username: kylelutz  
> Created_at: 2016-10-19 04:08:47 UTC  
> Url: https://github.com/boostorg/compute/pull/657#issuecomment-254705564  

Merged! Thanks!

---
