# #676 - Unexpected context is created by opengl_create_shared_context() [Closed]

> Username: sonogi1  
> Created at: 2016-12-20 08:15:37 UTC  
> Updated at: 2017-04-05 08:34:00 UTC  
> Closed at: 2017-04-05 08:34:00 UTC  
> Url: https://github.com/boostorg/compute/issues/676  

Computer: Intel CPU with HD Graphics.  
OS: Windows 10  
SDK: Intel SDK for OpenCL™ Applications 2016 R3  
  
Issue:   
The SDK includes two platforms, "Experimental OpenCL 2.1 CPU Only" and "Intel(R) OpenCL". The former CPU only platform is unexpectedly selected by opengl_create_shared_context. opengl_create_shared_context(platform const&) may be needed for manual platform selection.

---

## Comment 1

> Username: kenba  
> Created at: 2016-12-20 19:23:28 UTC  
> Url: https://github.com/boostorg/compute/issues/676#issuecomment-268333292  

Computer: AMD A10 with 6 Graphics cores and an Nvidia GeForce GTX 750 Ti Graphics Card  
OS: Windows 10  
SDKs: AMD APP SDK 3.0 and Intel SDK for OpenCL™ Applications 2016 R3  
  
Issue:  
The computer has multiple platforms, most of which support OpenGL...   
When calling opengl_create_shared_context() the program crashed during the call to "GetGLContextInfoKHR".  
  
According to [this](https://software.intel.com/en-us/articles/sharing-surfaces-between-opencl-and-opengl-43-on-intel-processor-graphics-using-implicit) article on Intel's site: "Careful reading of the specification highlights this is a platform extension not a device extension"...  
  
Simply adding the following lines fixed the crash on the AMD/Nvidia system and  enabled OpenGL/OpenCL sharing (see pull request #677):  
  
        if (!platform.supports_extension(cl_gl_sharing_extension))  
          continue;  
  
Note: opengl_create_shared_context() worked fine on a more "benign" system: an Intel Core i7-6500u  with an AMD R5 M335 graphics card also with both AMD and Intel SDKs and it still works with the fix above.
