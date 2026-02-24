# #791 - opengl_create_shared_context cl_gl_sharing_extension references are in quotes [Closed]

> Username: Javanater  
> Created at: 2018-07-24 02:20:51 UTC  
> Updated at: 2018-07-24 18:20:43 UTC  
> Closed at: 2018-07-24 18:20:43 UTC  
> Url: https://github.com/boostorg/compute/issues/791  

In interop/opengl/context.hpp, opengl_create_shared_context initializes the variable cl_gl_sharing_extension to "cl_APPLE_gl_sharing" for APPLE, and "cl_khr_gl_sharing" for all other platforms. The problem is that the cl_gl_sharing_extension variable is in quotes, so the function will always fail with a unsupported_extension_error. See lines 79, 121, and 132.

---

## Comment 1

> Username: kylelutz  
> Created at: 2018-07-24 04:51:47 UTC  
> Url: https://github.com/boostorg/compute/issues/791#issuecomment-407280522  

Hi @Javanater,  
  
I'm not sure I fully understand the issue. Can you explain problem you're encountering further (potentially with a reproducible test case demonstrating the failure)?  
  
Thanks!

---

## Comment 2

> Username: jszuppe  
> Created at: 2018-07-24 05:10:42 UTC  
> Url: https://github.com/boostorg/compute/issues/791#issuecomment-407283099  

I think it was fixed in https://github.com/boostorg/compute/pull/773.

---

## Comment 3

> Username: Javanater  
> Created at: 2018-07-24 18:20:40 UTC  
> Url: https://github.com/boostorg/compute/issues/791#issuecomment-407504021  

It was fixed in #773. Sorry, and Thanks.
