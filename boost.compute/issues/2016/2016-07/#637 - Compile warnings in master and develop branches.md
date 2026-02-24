# #637 - Compile warnings in master and develop branches [Closed]

> Username: dPavelDev  
> Created at: 2016-07-28 11:41:31 UTC  
> Updated at: 2016-08-16 19:23:03 UTC  
> Closed at: 2016-08-16 19:23:01 UTC  
> Url: https://github.com/boostorg/compute/issues/637  

When I tried to rebuild my project, I got some compile warnings in boost::compute sources:  
  
In master and develop branches:  
  
```  
e:\PROJECTS\compute\include\boost\compute\exception\context_error.hpp:73: warning: type qualifiers ignored on function return type [-Wignored-qualifiers]  
     const size_t get_private_info_size() const throw()  
                                                      ^  
```  
  
If remove const before size_t, warning is disappear but may be exist more right method.  
  
In develop branch:  
  
```  
e:\PROJECTS\compute\include\boost\compute\kernel.hpp:265: warning: unused parameter 'index' [-Wunused-parameter]  
     void set_arg_svm_ptr(size_t index, void* ptr)  
                                 ^  
```  
  
And ptr when not defined CL_VERSION_2_0.  
  
Also, when I rebuild project using MSVC (argument size_t size):  
  
```  
E:\PROJECTS\compute\include\boost\compute\detail\get_object_info.hpp:42: warning: C4267: 'argument': conversion from 'size_t' to 'cl_uint', possible loss of data  
```  
  
acquire.hpp:39, acquire.hpp:64, get_object_info.hpp:42  
  
```  
E:\PROJECTS\compute\include\boost\compute\interop\opengl\acquire.hpp:39: warning: C4267: 'argument': conversion from 'size_t' to 'cl_uint', possible loss of data  
```  
  
On x64 size_t != cl_uint. It is necessary to fix this warnings?

---

## Comment 1

> Username: kylelutz  
> Created at: 2016-07-31 01:34:57 UTC  
> Updated at: 2016-07-31 01:35:44 UTC  
> Url: https://github.com/boostorg/compute/issues/637#issuecomment-236401784  

Thanks for reporting this! Most of these should be fixed with PR #638. Would you mind testing that out?

---

## Comment 2

> Username: dPavelDev  
> Created at: 2016-07-31 09:03:50 UTC  
> Url: https://github.com/boostorg/compute/issues/637#issuecomment-236419305  

Good job! Only 1 warning:  
  
```  
E:\PROJECTS\boostorg\compute\include\boost\compute\detail\get_object_info.hpp:42: warning: C4267: 'argument': conversion from 'size_t' to 'cl_uint', possible loss of data  
```

---

## Comment 3

> Username: kylelutz  
> Created at: 2016-08-03 04:40:00 UTC  
> Url: https://github.com/boostorg/compute/issues/637#issuecomment-237136304  

Hmm, is there any sort of "back-trace" for that warning? I'd like to see where it's getting called from (the `get_object_info()` function is a wrapper for many different `clGet*Info()` calls, most of which use `size_t` instead of `cl_uint`).

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-08-03 08:18:26 UTC  
> Url: https://github.com/boostorg/compute/issues/637#issuecomment-237173622  

I've found this. `kernel.hpp`, lines 191 and 268, `index` has to be cast to `cl_uint`, and there's no warnings after that.

---

## Comment 5

> Username: kylelutz  
> Created at: 2016-08-04 03:17:45 UTC  
> Url: https://github.com/boostorg/compute/issues/637#issuecomment-237441594  

Ahh, got it. This should be fixed in PR #638 now.
