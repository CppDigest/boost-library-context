# #500 - GetGLContextInfoKHR() crashes (Windows) [Closed]

> Username: buggybugger  
> Created at: 2015-08-24 18:05:35 UTC  
> Updated at: 2016-07-28 04:16:55 UTC  
> Closed at: 2016-07-28 04:16:49 UTC  
> Url: https://github.com/boostorg/compute/issues/500  

I've worked on an application that make an OpenGL/OpenCL interop on linux (ubuntu 14.10) using Boost.Compute. The application was running fine on linux until the nvidia drivers update.  
The application uses :   
GLFW3, MATIO, GLEW, Boost.Compute, clFFT, LUA5.2  
  
Now I'm porting the application on Windows 8.1 but the code working perfectly on linux just crashes on windows.  
  
IDE : Visual Studio 12 (2013) 64bits  
  
Graphic cards tested :   
- NVIDIA GeForce 750M (laptop)  
- NVIDIA Titan X (workstation)  
  
I've tried  
GLEW, GL3W, glloadgen, glad for OpenGL  
and  
intel INDE 2015 Update 2, NVIDIA (CUDA 7.0) for OpenCL  
  
**VS gives me** :   
Unhandled exception at 0x00000000CCD82A90 in Hologram.exe: 0xC0000005: Access violation executing location 0x00000000CCD82A90.  
  
The breakpoint is set to this line in context.hpp  
  
``` c  
cl_int ret = GetGLContextInfoKHR(  
            properties,  
            CL_CURRENT_DEVICE_FOR_GL_CONTEXT_KHR,  
            sizeof(cl_device_id),  
            &gpu_id,  
            0  
        );  
```  
  
from the function compute::opengl_create_shared_context();  
  
Locals :   
  
```  
+       gpu {m_id=0xcccccccccccccccc {...} }    boost::compute::device  
        ret -858993460  int  
        gpu_id  0xcccccccccccccccc {...}    _cl_device_id *  
        GetGLContextInfoKHR 0x00000000ccd82a90  int (const __int64 *, unsigned int, unsigned __int64, void *, unsigned __int64 *) *  
+       properties  0x00000084e77bf1a8 {4228, 570856697760, 8200, 65536, 8203, 285289982, 0}    __int64[7]  
+       platform    {m_platform=0x00000084e9b5efa0 {...} }  const boost::compute::platform &  
        i   0   unsigned __int64  
+       cl_gl_sharing_extension 0x00007ff75455d7f8 "cl_khr_gl_sharing"  const char *  
+       platforms   { size=2 }  std::vector<boost::compute::platform,std::allocator<boost::compute::platform> >  
```  
  
CallStack :   
  
```  
    00000000ccd82a90()  Unknown  
>   Hologram.exe!boost::compute::opengl_create_shared_context() Line 116    C++  
    Hologram.exe!Interop::lua_initKernel(std::basic_string<char,std::char_traits<char>,std::allocator<char> > kernelName) Line 141  C++  
    Hologram.exe!Interop::clua_initKernel(lua_State * L) Line 9 C++  
    lua52.dll!lua_getinfo() + 4562 bytes    Unknown  
    lua52.dll!00007ffcc6419846()    Unknown  
    lua52.dll!lua_callk() + 374 bytes   Unknown  
    lua52.dll!lua_getinfo() + 2962 bytes    Unknown  
    lua52.dll!lua_yieldk() + 212 bytes  Unknown  
    lua52.dll!lua_pcallk() + 388 bytes  Unknown  
    Hologram.exe!LuaWrapper::run() Line 48  C++  
    Hologram.exe!main() Line 181    C++  
    [External Code]   
```  
  
EDIT: Simplified version of main  
  
``` c++  
#include <iostream>  
#include <stdlib.h>  
#include <stdio.h>  
#ifdef _WIN32  
  #define GLFW_DLL  
#endif  
#include <GL/glew.h>  
#include <GLFW/glfw3.h>  
#include "Interop.h"  
  
int main(void)  
{  
  GLFWwindow* window;  
  if (!glfwInit())  
    exit(EXIT_FAILURE);  
  glfwSetErrorCallback(error_callback);  
  
  const GLFWvidmode * mode = glfwGetVideoMode(glfwGetPrimaryMonitor());  
  width = mode->width;  
  height = mode->height;  
  
  window = glfwCreateWindow(width, height, "Hologram", glfwGetPrimaryMonitor(), NULL);  
  
  if (!window)  
  {  
    glfwTerminate();  
    exit(EXIT_FAILURE);  
  }  
  glfwMakeContextCurrent(window);  
  
  // Initialise GLEW  
  if (glewInit() != GLEW_OK) {  
    fprintf(stderr, "Failed to initialize GLEW\n");  
    abort();  
  }  
  
  glfwSwapInterval(1);  
  glfwSetKeyCallback(window, key_callback);  
  glfwSetCharCallback(window, char_callback);  
  glfwSetMouseButtonCallback(window, mouseButton_callback);  
  glfwSetScrollCallback(window, scroll_callback);  
  glfwSetCursorEnterCallback(window, cursorEnter_callback);  
  
  Interop* interop = NULL;  
  
  while (!glfwWindowShouldClose(window))  
  {  
    glfwPollEvents();  
    int width, height;  
    glfwGetFramebufferSize(window, &width, &height);  
    if (menus->isSetFlag(menus->flag_hologramChanged)) {  
      delete interop;  
      interop = new Interop(width, height);  
      [...]  
    }  
[...]  
  }  
  glfwDestroyWindow(window);  
  glfwTerminate();  
  delete interop;  
  exit(EXIT_SUCCESS);  
}  
```  
  
Thanks.

---

## Comment 1

> Username: rhoarau  
> Created at: 2016-01-11 17:03:59 UTC  
> Updated at: 2016-01-11 17:05:49 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-170618993  

Bump, I have exactly the same problem. The function "GetGLContextInfoKHR" (clGetGLContextInfoKHR) crashed. I thought at first it was due to the shitty opencl implementation of nvidia, but even on a amd, It doesn't work.   
  
I have tested theOpenCL-OpenGL sample from Nvidia and it work perfectly (they don't use clGetGLContextInfoKHR:http://developer.download.nvidia.com/compute/cuda/4_2/rel/sdk/website/OpenCL/html/samples.html) .   
  
IDE : Visual Studio 14 (2015) 64bits  
  
Graphics Cards tested:  
Quadro 2000  
Geforce gtx880m  
AMD HD7970

---

## Comment 2

> Username: dPavelDev  
> Created at: 2016-07-25 14:09:33 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-234963736  

Ohhh, I have similar problem on Intel HD Graphics 530. How to solve it?

---

## Comment 3

> Username: rhoarau  
> Created at: 2016-07-26 19:28:03 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-235377970  

@dPavelDev   
  
I just remove the clGetGLContextInfoKHR:  
  
```  
compute::context createOCLContextFromOGLContext(const compute::device& device, const OGLContext& oglContext) {  
        compute::context context;  
        cl_device_id rawDevice = device.get();  
        GLFWwindow* previousContext = glfwGetCurrentContext();  
        oglContext.enable();  
  
        #if defined(__APPLE__)  
            // get OpenGL share group  
            CGLContextObj cgl_current_context = CGLGetCurrentContext();  
            cl_context_properties properties[] = {  
                CL_CONTEXT_PROPERTY_USE_CGL_SHAREGROUP_APPLE,  
                (cl_context_properties)CGLGetShareGroup(cgl_current_context),  
                0  
            };  
  
            cl_int error = 0;  
            cl_context cl_gl_context = clCreateContext(properties, 0, 0, 0, 0, &error);  
            if (!cl_gl_context)  
                throw OCLException(compute::opencl_error::to_string(result));  
  
            context = compute::context(cl_gl_context, false);  
        #else  
            cl_context_properties properties[] = {  
                CL_CONTEXT_PLATFORM, (cl_context_properties)device.platform().id(),  
                #if defined(__linux__)  
                    CL_GL_CONTEXT_KHR, (cl_context_properties)glXGetCurrentContext(),  
                    CL_GLX_DISPLAY_KHR, (cl_context_properties)glXGetCurrentDisplay(),  
                #elif defined(WIN32)  
                    CL_GL_CONTEXT_KHR, (cl_context_properties)wglGetCurrentContext(),  
                    CL_WGL_HDC_KHR, (cl_context_properties)wglGetCurrentDC(),  
                #endif  
                0  
            };  
  
            if (!device.supports_extension("cl_khr_gl_sharing"))  
                throw OCLException("cl_khr_gl_sharing is not supported by the device");  
  
            context = compute::context(device, properties);  
  
        #endif  
  
        oglContext.disable();  
        glfwMakeContextCurrent(previousContext);  
  
        return context;  
    }  
```  
  
OGLContext is just a wrapper for GLFWwindow (GLFW).  
I didn't test this code on MacOS, and now on Linux my intel HD don't have the cl_khr_gl_sharing extension to test it.

---

## Comment 4

> Username: dPavelDev  
> Created at: 2016-07-27 10:49:09 UTC  
> Updated at: 2016-07-27 10:54:24 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-235551749  

Ops.. What is it? In file context.hpp:  
  
``` cpp  
GetGLContextInfoKHRFunction GetGLContextInfoKHR =  
            reinterpret_cast<GetGLContextInfoKHRFunction>(  
                reinterpret_cast<unsigned long>(  
                    platform.get_extension_function_address("clGetGLContextInfoKHR")  
                )  
            );  
```  
  
When I replace it:  
  
``` cpp  
GetGLContextInfoKHRFunction GetGLContextInfoKHR =  
            reinterpret_cast<GetGLContextInfoKHRFunction>(  
                //reinterpret_cast<unsigned long>(  
                    platform.get_extension_function_address("clGetGLContextInfoKHR")  
                //)  
            );  
```  
  
This works fine on my machine (Windows 10 x64, MSVC 2015 x64). Who wants to answer this question: Why do we need this construction? MSVC swear on it.  
  
Aaaaaa, lol. Unsigned long long :). @kylelutz , fix this bug!

---

## Comment 5

> Username: ddemidov  
> Created at: 2016-07-27 10:54:23 UTC  
> Updated at: 2016-07-27 10:54:53 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-235552730  

`sizeof(unsigned long) == 4` in MSVC, which is not enough to hold a pointer on a 64bit system. Possible solution could be to switch to `size_t` in `reinterpret_cast`.

---

## Comment 6

> Username: dPavelDev  
> Created at: 2016-07-27 10:54:46 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-235552799  

Yes, it is bug!

---

## Comment 7

> Username: jszuppe  
> Created at: 2016-07-27 11:04:24 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-235554547  

@dPavelDev, can you make a pull request for it or should I do it?

---

## Comment 8

> Username: dPavelDev  
> Created at: 2016-07-27 11:18:41 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-235557147  

Yes, I've already created a pull request.

---

## Comment 9

> Username: jszuppe  
> Created at: 2016-07-27 11:21:06 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-235557592  

Great! Thanks! :+1:

---

## Comment 10

> Username: kylelutz  
> Created at: 2016-07-28 04:16:49 UTC  
> Url: https://github.com/boostorg/compute/issues/500#issuecomment-235796806  

Fixed in PR #636. Thanks @dPavelDev!
