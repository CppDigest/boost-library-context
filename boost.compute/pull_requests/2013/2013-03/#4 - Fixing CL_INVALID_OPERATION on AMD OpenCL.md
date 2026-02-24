# #4 Fixing CL_INVALID_OPERATION on AMD OpenCL [Closed]

> Username: ddemidov  
> Created at: 2013-03-22 07:00:10 UTC  
> Updated at: 2014-07-11 02:40:47 UTC  
> Closed at: 2013-03-22 20:20:03 UTC  
> Url: https://github.com/boostorg/compute/pull/4  

`boost::compute::program::build()` throws `CL_INVALID_OPERATION` when run on AMD OpenCL implementation.   
  
I believe this is the same issue as reported here: http://devgurus.amd.com/thread/159987.  
  
The Khronos C++ bindings only retain programs on copy, not on creation (so normally program lifecycle is build, release, not retain, build, release). The proposed patch fixes the issue by combining `program::create_with_source/binary` and `program::build`.  
  
With the patch applied, the only failing tests on AMD OpenCL (with CPU as compute device) are these:  
  
```  
The following tests FAILED:  
      2 - core.command_queue (Failed)  
     32 - algorithm.insertion_sort (Failed)  
     39 - algorithm.radix_sort (Failed)  
     47 - algorithm.sort (Failed)  
```

---

## Comment 1

> Username: kylelutz  
> Created_at: 2013-03-22 20:20:03 UTC  
> Url: https://github.com/boostorg/compute/pull/4#issuecomment-15319529  

Hi Dennis,  
  
Interesting. Thanks for tracking down the bug!  
  
However, I'd like to keep the program::create_with_\* methods separate from the program::build() method (especially with the new compile and link features in OpenCL 1.2). Also, I've been trying to keep a fairly close 1:1 mapping between the OpenCL C API and the corresponding functions in the Boost.Compute wrapper. For these reasons I'm not comfortable with this patch combining the create and build steps.  
  
I'll think about some other ways to work-around the AMD bug without changing the program API. If you have any ideas/suggestions please let me know.  
  
Thanks,  
Kyle

---

## Comment 2

> Username: kylelutz  
> Created_at: 2013-03-22 20:38:33 UTC  
> Url: https://github.com/boostorg/compute/pull/4#issuecomment-15320320  

As for the second patch (bf7e0e9), I like the idea but I think it would be better to implement it using a move constructor for program (taking cl_program&&).

---

## Comment 3

> Username: ddemidov  
> Created_at: 2013-03-23 03:03:10 UTC  
> Url: https://github.com/boostorg/compute/pull/4#issuecomment-15330780  

> However, I'd like to keep the program::create_with_\* methods separate from the program::build() method (especially with the new compile and link features in OpenCL 1.2).  
  
Ok, I understand.  
  
> As for the second patch (bf7e0e9), I like the idea but I think it would be better to implement it using a move constructor for program (taking cl_program&&).  
  
I like that, but that would _require_ compilation with -std=c++11 flag to be usable with AMD OpenCL. Also, call to program(cl_program&&) is ambiguous:  
  
```  
include/boost/compute/program.hpp|241 col 43| error: call of overloaded ‘program(std::remove_reference<_cl_program*&>::type)’ is ambiguous  
include/boost/compute/program.hpp|241 col 43| note: candidates are:  
include/boost/compute/program.hpp|60 col 14| note: boost::compute::program::program(boost::compute::program&&)  
include/boost/compute/program.hpp|55 col 14| note: boost::compute::program::program(_cl_program*&&)  
include/boost/compute/program.hpp|46 col 5| note: boost::compute::program::program(const boost::compute::program&)  
include/boost/compute/program.hpp|38 col 14| note: boost::compute::program::program(cl_program)  
```  
  
And this could break things in places like [this](https://github.com/kylelutz/compute/blob/master/include/boost/compute/kernel.hpp#L96), where program should be retained, but move constructor would be picked.

---

## Comment 4

> Username: ddemidov  
> Created_at: 2013-03-23 06:30:28 UTC  
> Url: https://github.com/boostorg/compute/pull/4#issuecomment-15332612  

How about this: da16bd2fa477cb0f5c0331f1dbdad4b4fe50a4e0? It works on AMD, all tests except the above four are passing.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2013-03-26 01:28:26 UTC  
> Url: https://github.com/boostorg/compute/pull/4#issuecomment-15436019  

I like this a lot better! And it's extensible to all the other wrapper classes.  
  
I've cherry-picked it into my master branch. Can you run the tests on AMD again and post the output of those that fail?  
  
Thanks,  
-kyle

---
