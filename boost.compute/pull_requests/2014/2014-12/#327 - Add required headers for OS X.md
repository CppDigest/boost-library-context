# #327 Add required headers for OS X [Closed]

> Username: kwonoh  
> Created at: 2014-12-04 00:18:57 UTC  
> Updated at: 2014-12-06 20:35:33 UTC  
> Closed at: 2014-12-06 20:35:26 UTC  
> Url: https://github.com/boostorg/compute/pull/327  



---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-04 00:36:21 UTC  
> Url: https://github.com/boostorg/compute/pull/327#issuecomment-65518757  

[![Coverage Status](https://coveralls.io/builds/1563488/badge)](https://coveralls.io/builds/1563488)  
  
Coverage remained the same when pulling **45d5826fda181c7f7dbe877e6525e54ce053fa1e on kwonoh:patch-1** into **fa75a66253a759e15121a9b74d4db273448137cd on kylelutz:master**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-12-04 06:13:21 UTC  
> Url: https://github.com/boostorg/compute/pull/327#issuecomment-65542509  

Thanks for the patch!  
  
However, to be a better fit with the existing headers, I'd like to change this slightly. Currently, the forwarding headers (like `<boost/compute/cl.hpp>`) forward to the single corresponding OpenCL header (e.g. `<CL/cl.h>` for Linux and `<OpenCL/cl.h>` for Apple). For these two new headers, could you instead create individual forwarding headers in the `<boost/compute/interop/opengl/...>` directory like this:  
- `<boost/compute/interop/opengl/gl.hpp>` would forward to `<GL/gl.h>` or `<OpenGL/gl.h>`  
- `<boost/compute/interop/opengl/cl_gl_ext.hpp>` would forward to `<CL/cl_gl_ext.h>` or `<OpenGL/cl_gl_ext.h>`  
  
You should probably be able to copy-paste the [`<boost/compute/interop/opengl/cl_gl.hpp>`](https://github.com/kylelutz/compute/blob/master/include/boost/compute/interop/opengl/cl_gl.hpp) header as a template for these new headers.  
  
Also, could you make the pull-request against the `develop` branch? Instructions for this can be found in the [Developer's Guide](https://github.com/kylelutz/compute/wiki/Developer%27s-Guide#submitting-patches) on the wiki. Thanks!

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-12-06 20:18:44 UTC  
> Url: https://github.com/boostorg/compute/pull/327#issuecomment-65912123  

I made a pull-request to add the `cl_gl_ext.hpp` header (PR #332). Also, it looks like the `gl.hpp` header is already available in `<boost/compute/interop/opengl/...>` (see https://github.com/kylelutz/compute/blob/master/include/boost/compute/interop/opengl/gl.hpp).

---

## Comment 4

> Username: kwonoh  
> Created_at: 2014-12-06 20:26:03 UTC  
> Updated_at: 2014-12-06 20:28:03 UTC  
> Url: https://github.com/boostorg/compute/pull/327#issuecomment-65912412  

`<OpenGL/gl.h>` is not enough for OS X to interop between CL-GL. `<OpenGL/OpenGL.h>` (it's like `glx` and `wgl`) has the required symbols like `CGLContextObj`, `CGLGetCurrentContext`.   
So where would be an appropriate location to include the header?

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-12-06 20:29:37 UTC  
> Url: https://github.com/boostorg/compute/pull/327#issuecomment-65912563  

Ahh, I see. In that case I think it would be best to add it under the "`#ifdef __APPLE__`" section in `<boost/compute/interop/opengl/gl.hpp>`.

---
