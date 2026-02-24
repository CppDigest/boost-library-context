# #677 Check platform extension for issue #676 [Merged]

> Username: kenba  
> Created at: 2016-12-20 19:04:56 UTC  
> Updated at: 2017-03-25 12:58:30 UTC  
> Merged at: 2017-03-25 12:58:30 UTC  
> Closed at: 2017-03-25 12:58:30 UTC  
> Url: https://github.com/boostorg/compute/pull/677  

Check that a platform supports OpenCL/OpenGL sharing before attempting to load the clGetGLContextInfoKHR() extension function in function opengl_create_shared_context().

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-12-20 20:22:38 UTC  
> Url: https://github.com/boostorg/compute/pull/677#issuecomment-268348436  

[![Coverage Status](https://coveralls.io/builds/9366299/badge)](https://coveralls.io/builds/9366299)  
  
Coverage remained the same at 83.338% when pulling **e2f998bec6eb16ed77cd445c3bf0bb6fac601e93 on kenba:master** into **82f588f24ed2640bca4bf3f31dfcb06703d9bc13 on boostorg:master**.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2017-03-25 12:58:13 UTC  
> Url: https://github.com/boostorg/compute/pull/677#issuecomment-289210157  

I think OSX build keeps failing only because some bug in image used by Travis CI. It worked ok when I rebased it and pushed to my fork https://travis-ci.org/haahh/compute/builds/214949240.

---
