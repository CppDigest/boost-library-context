# #328 - OpenGL Interop in OS X [Closed]

> Username: kwonoh  
> Created at: 2014-12-04 00:37:19 UTC  
> Updated at: 2015-02-26 22:37:05 UTC  
> Closed at: 2015-02-26 22:37:01 UTC  
> Url: https://github.com/boostorg/compute/issues/328  

Several compile time errors occur in function [opengl_create_shared_context()](https://github.com/kylelutz/compute/blob/master/include/boost/compute/interop/opengl/context.hpp#L37)  on OS X (10.10.1, Xcode 6.1.1) when I try to compile [opengl_sphere example](https://github.com/kylelutz/compute/blob/master/example/opengl_sphere.cpp).  
  
the errors looks like below:  
  
```  
../../../local/include/boost/compute/interop/opengl/context.hpp:59:35: error: C++ requires a type specifier for all declarations  
    const cl_context_properties*, cl_gl_context_info, size_t, void*, size_t*);  
                                  ^~~~~~~~~~~~~~~~~~  
../../../local/include/boost/compute/interop/opengl/context.hpp:76:5: error: unknown type name 'CGLContextObj'  
    CGLContextObj cgl_current_context = CGLGetCurrentContext();  
    ^  
../../../local/include/boost/compute/interop/opengl/context.hpp:76:41: error: use of undeclared identifier 'CGLGetCurrentContext'  
    CGLContextObj cgl_current_context = CGLGetCurrentContext();  
                                        ^  
../../../local/include/boost/compute/interop/opengl/context.hpp:77:5: error: unknown type name 'CGLShareGroupObj'  
    CGLShareGroupObj cgl_share_group = CGLGetShareGroup(cgl_current_context);  
    ^  
../../../local/include/boost/compute/interop/opengl/context.hpp:90:7: error: use of undeclared identifier 'CL_CONTEXT_PROPERTY_USE_CGL_SHAREGROUP_APPLE'  
      CL_CONTEXT_PROPERTY_USE_CGL_SHAREGROUP_APPLE,  
      ^  
../../../local/include/boost/compute/interop/opengl/context.hpp:104:39: error: use of undeclared identifier 'CL_CURRENT_DEVICE_FOR_GL_CONTEXT_KHR'  
      GetGLContextInfoKHR(properties, CL_CURRENT_DEVICE_FOR_GL_CONTEXT_KHR,  
```  
  
I handle the errors as follows:  
1. [Add required headers](https://github.com/kylelutz/compute/pull/327)  
2. I used below code to create shared context.  
But it need to be done with more appropriate way. (e.g., checking GL context has been created, checking CL-GL sharing capable device)  
  
``` cpp  
inline context opengl_create_shared_context()  
#if defined(__APPLE__)  
{  
  CGLContextObj kCGLContext = CGLGetCurrentContext();  
  CGLShareGroupObj kCGLShareGroup = CGLGetShareGroup(kCGLContext);  
  
  cl_context_properties properties[] = {  
    CL_CONTEXT_PROPERTY_USE_CGL_SHAREGROUP_APPLE,  
    (cl_context_properties)kCGLShareGroup, 0};  
  
  return context(clCreateContext(properties, 0, 0, NULL, 0, 0));  
}  
#else  
// existing codes ...  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-04 06:23:15 UTC  
> Url: https://github.com/boostorg/compute/issues/328#issuecomment-65543073  

Interesting, this seems like it can't find some symbols due to missing headers. If you include the proper `gl.h` and `cl_gl_ext.h` headers does it compile?  
  
Also, the code in [opengl_create_shared_context()](https://github.com/kylelutz/compute/blob/master/include/boost/compute/interop/opengl/context.hpp#L66) should take care of the Apple case (very similar to the code you provided, see around line 66).  
  
Unfortunately, I don't have regular access to a Mac machine so I can't test this code. Thanks for testing it out and fixing it!

---

## Comment 2

> Username: kwonoh  
> Created at: 2014-12-04 06:39:36 UTC  
> Updated at: 2014-12-04 06:41:57 UTC  
> Url: https://github.com/boostorg/compute/issues/328#issuecomment-65544160  

If I include `<OpenCL/cl_gl_ext.h>` and `<OpenGL/OpenGL.h>` then the missing symbols are found except `cl_gl_context_info` and `CL_CURRENT_DEVICE_FOR_GL_CONTEXT_KHR`.  
  
Anyway, I saw the code around line 66, but I don't know how to resolve other errors occurred from the above two missing symbols.   
So after some googling I narrow it down to the minimal required code to create shared context.  
And [this](https://qt.gitorious.org/qt-labs/qt-opencl-mac-os-x-fixes/archive/adc4ecb4bae67f585cbdc4d0115edb59c288df9f.tar.gz,%20https://qt.gitorious.org/qt-labs/qt-opencl-mac-os-x-fixes/source/adc4ecb4bae67f585cbdc4d0115edb59c288df9f:src/openclgl/qclcontextgl.cpp) looks helpful to resolve this issue.  
  
I just started using `OpenCL` and `boost.compute` since yesterday. Please understand my short knowledge about this.

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-12-04 06:55:58 UTC  
> Url: https://github.com/boostorg/compute/issues/328#issuecomment-65545216  

Interesting, those symbols are both defined in `cl_gl.h` for me on my Linux machine. Where are they defined on Apple?  
  
And thanks for working on this! Let me know if I can do anything to help. You may also try posting to the mailing list (https://groups.google.com/forum/#!forum/boost-compute) and see if others running Mac OS X can test out the code and/or offer suggestions.

---

## Comment 4

> Username: roshanrags  
> Created at: 2014-12-04 17:02:34 UTC  
> Url: https://github.com/boostorg/compute/issues/328#issuecomment-65664836  

I can test it, but the OpenGL example is not there in my Makefile. Something to be enabled in `cmake`?

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-12-04 17:31:01 UTC  
> Url: https://github.com/boostorg/compute/issues/328#issuecomment-65669406  

There are a few OpenGL examples. To compile them you need to enable `BOOST_COMPUTE_HAVE_QT` and/or `BOOST_COMPUTE_HAVE_VTK` in `cmake`.

---

## Comment 6

> Username: roshanrags  
> Created at: 2014-12-06 11:17:24 UTC  
> Url: https://github.com/boostorg/compute/issues/328#issuecomment-65893473  

`cl_gl_context_info` and `CL_CURRENT_DEVICE_FOR_GL_CONTEXT_KHR` are missing from the `OpenGL` as well as `OpenCL` frameworks. None of the headers contain them... From the OpenCL reference card, `CL_CURRENT_DEVICE_FOR_GL_CONTEXT_KHR` seems to be optional, so maybe it is not there on OS X.   
Further, the `clGetGLContextInfoKHR` function as a whole is missing from the OpenCL framework(it should have been there?).  
  
The link shared above by @kwonoh also seems to indicate something similar near lines 222 and 262...

---

## Comment 7

> Username: kylelutz  
> Created at: 2014-12-06 20:03:42 UTC  
> Url: https://github.com/boostorg/compute/issues/328#issuecomment-65911602  

Yeah, `clGetGLContextInfoKHR()` is a extension function and may not be available everywhere. I based the implementation mainly on the documentation for the [`cl_khr_gl_sharing`](https://www.khronos.org/registry/cl/sdk/1.2/docs/man/xhtml/cl_khr_gl_sharing.html) extension.  
  
Getting this to work reliably on OS X would be great. Have either of you guys had luck following the Apple documentation for this?

---

## Comment 8

> Username: kylelutz  
> Created at: 2015-02-08 01:13:22 UTC  
> Url: https://github.com/boostorg/compute/issues/328#issuecomment-73391718  

Alright, this should be fixed in PR #425. Can you guys try that out?

---

## Comment 9

> Username: kylelutz  
> Created at: 2015-02-26 22:37:01 UTC  
> Url: https://github.com/boostorg/compute/issues/328#issuecomment-76290191  

This should be resolved as of d4adfc5feb4eb38eaf2786a7de00b74065cfa753. Please re-open if you still have issues.
