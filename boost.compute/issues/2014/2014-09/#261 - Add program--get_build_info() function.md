# #261 - Add program::get_build_info() function [Closed]

> Username: kylelutz  
> Created at: 2014-09-13 17:47:45 UTC  
> Updated at: 2014-09-15 04:13:44 UTC  
> Closed at: 2014-09-15 04:13:44 UTC  
> Url: https://github.com/boostorg/compute/issues/261  

Add a `program::get_build_info()` function which wraps the [`clGetProgramBuildInfo()`](http://www.khronos.org/registry/cl/sdk/1.0/docs/man/xhtml/clGetProgramBuildInfo.html) function.  
  
This is a bit more complicated than using the existing `detail::get_object_info()` function as the `clGetProgramBuildInfo()` function also takes a device argument.  
  
The API should look something like this:  
  
```  
class program  
{  
    ...  
  
    template<class T>  
    T get_build_info(cl_program_build_info info, const device &device)  
    {  
    }  
}  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-09-14 01:43:38 UTC  
> Url: https://github.com/boostorg/compute/issues/261#issuecomment-55512711  

Implemented in PR #264.
