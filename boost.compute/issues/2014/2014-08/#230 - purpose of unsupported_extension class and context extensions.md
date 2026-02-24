# #230 - purpose of unsupported_extension class and context extensions [Open]

> Username: f-koehler  
> Created at: 2014-08-11 10:12:54 UTC  
> Updated at: 2017-04-10 19:19:17 UTC  
> Url: https://github.com/boostorg/compute/issues/230  

The `unsupported_extension` exception is currently only thrown by `opengl_create_shared_context()`. Maybe one could also throw it when the constructor of a general `context` fails, if there is an unsupported extension specified by `const cl_context_properites *properties`.  
  
Maybe there also should be a way to construct contexts with extensions without having to create `0`-terminated lists by hand (maybe a vector class where the user can add extensions).  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-12 03:30:29 UTC  
> Url: https://github.com/boostorg/compute/issues/230#issuecomment-51869619  

The idea was to provide certain functions/classes (like the OpenGL interop APIs) but throw an `unsupported_extension_error` exception if the underyling compute device didn't support the extension (which is only detectable at run-time and thus we use exceptions). For reference, you can see the full list of current OpenCL extensions here: http://www.khronos.org/registry/cl/ (at the bottom of the page).  
  
Currently `clCreateContext()` will throw an `opencl_error` exception on failure (which will contain the error code/string). It could be interesting to throw an `unsupported_extension_error` instead if we can detect that `clCreateContext()` failed due to the device lacking support for an extension. Can you give me some examples of `cl_context_properties` values and return codes from `clCreateContext()` you would use to check if you should throw the `unsupported_extension_error` exception instead of the regular `opencl_error` exception?  
  
And i really like the idea of supporting a `context` constructor which takes a `std::vector<cl_context_properties>` argument! That would make it much more natural to use from c++. and with c++11 they could use intializer lists and do something like this:  
  
```  
boost::compute::context ctx(device, {CL_GL_CONTEXT_KHR, glXGetCurrentContext()});  
```  
  
It would also be nice to offer the same API for creating command queues with properties:  
  
```  
boost::compute::command_queue queue(ctx, dev, {CL_QUEUE_PROFILING_ENABLE});  
```

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-04-10 19:19:17 UTC  
> Url: https://github.com/boostorg/compute/issues/230#issuecomment-293051220  

After looking at `clCreateContext` I think in general we can't throw an `unsupported_extension_error` from `boost::compute::context`, because there is no special error code for unsupported extension error in OpenCL. `CL_INVALID_PROPERTY` that is returned by `clCreateContext` when extension is not supported is also returned for other errors.  
  
>CL_INVALID_PROPERTY - if context property name in properties is not a supported property name, if the value specified for a supported property name is not valid, or if the same property name is specified more than once. However if the extension cl_khr_gl_sharing is enabled, then CL_INVALID_PROPERTY is returned if an attribute name other than those listed in the table for properties above or if CL_CONTEXT_INTEROP_USER_SYNC is specified in properties.  
  
> CL_INVALID_PROPERTY - if the cl_khr_gl_sharing extension is enabled and an attribute name other than those specified in the table above (table 4.5) or if CL_CONTEXT_INTEROP_USER_SYNC is specified in properties.
