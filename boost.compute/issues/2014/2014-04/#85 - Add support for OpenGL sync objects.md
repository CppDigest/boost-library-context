# #85 - Add support for OpenGL sync objects [Open]

> Username: kylelutz  
> Created at: 2014-04-18 03:14:07 UTC  
> Updated at: 2017-03-21 04:14:55 UTC  
> Url: https://github.com/boostorg/compute/issues/85  

Add an `opengl_sync_event` class which supports creating OpenCL events from OpenGL sync objects using the [clCreateEventFromGLsyncKHR()](https://www.khronos.org/registry/cl/sdk/1.2/docs/man/xhtml/clCreateEventFromGLsyncKHR.html) function. It should inherit from the `boost::compute::event` class so that it can be used just like any other event.  
##
