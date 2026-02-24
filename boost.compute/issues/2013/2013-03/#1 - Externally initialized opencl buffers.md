# #1 - Externally initialized opencl buffers [Closed]

> Username: ddemidov  
> Created at: 2013-03-19 06:26:19 UTC  
> Updated at: 2013-03-21 04:21:26 UTC  
> Closed at: 2013-03-20 11:53:53 UTC  
> Url: https://github.com/boostorg/compute/issues/1  

Hello Kyle!  
  
Is it possible to use externally initialized OpenCL contexts, queues, and buffers with Boost.Compute algorithms?  
  
It seems that it should be possible with boost::compute::make_buffer_iterator(), but boost::compute::buffer only provides protected constructor from cl_mem.  
  
Best regards,  
Denis

---

## Comment 1

> Username: byzhang  
> Created at: 2013-03-19 06:28:58 UTC  
> Url: https://github.com/boostorg/compute/issues/1#issuecomment-15099421  

It's helpful to allow using multiple opencl libraries (w/o copying data back and forth)

---

## Comment 2

> Username: ddemidov  
> Created at: 2013-03-19 13:29:38 UTC  
> Url: https://github.com/boostorg/compute/issues/1#issuecomment-15113928  

I've tried to add the constructors to command_queue and buffer: 6766b07fade05bd3bcf9c7324a17b4804c69b276  
  
But this simple example fails for me:  
  
``` C++  
#include <vexcl/vexcl.hpp>  
#include <boost/compute.hpp>  
  
int main() {  
    const size_t n = 1024;  
  
    vex::Context ctx( vex::Filter::Env && vex::Filter::Count(1) );  
    std::cout << ctx << std::endl;  
  
    vex::vector<double> x(ctx, n);  
    x = 1;  
  
    boost::compute::command_queue cq(  
            /* this returns cl_command_queue: */ ctx.queue(0)()  
            );  
  
    boost::compute::buffer cb(  
            /*this returns cl_mem:*/ x(0)()  
            );  
  
    // The following throws boost::compute::runtime_exception  
    // "Invalid Operation"  
    std::cout  
        << boost::compute::accumulate(  
                boost::compute::make_buffer_iterator<double>(cb, 0),  
                boost::compute::make_buffer_iterator<double>(cb, n),  
                0.0, cq  
                )  
        << std::endl;  
}  
```

---

## Comment 3

> Username: ddemidov  
> Created at: 2013-03-19 19:09:46 UTC  
> Url: https://github.com/boostorg/compute/issues/1#issuecomment-15136479  

Hmm, I only get this error when running with AMD OpenCL. The example works fine with NVIDIA's and Intel's OpenCL implementations.

---

## Comment 4

> Username: kylelutz  
> Created at: 2013-03-19 21:24:34 UTC  
> Url: https://github.com/boostorg/compute/issues/1#issuecomment-15144140  

Hmm. Can you get a backtrace showing which OpenCL function returns the `CL_INVALID_OPERATION` error?

---

## Comment 5

> Username: ddemidov  
> Created at: 2013-03-19 21:29:35 UTC  
> Url: https://github.com/boostorg/compute/issues/1#issuecomment-15144438  

Here is output from test_scan on AMD's Capeverde:  
  
```  
$ ./build/test/test_scan   
Running 6 test cases...  
Boost.Compute: kernel compilation failed (-59)  
--- source ---  
  
__kernel void copy(__global int* _buf0)  
{  
const uint i = get_global_id(0);  
_buf0[i]=0;  
  
}  
  
--- build log ---  
  
/home/demidov/work/opencl/compute/include/boost/compute/program.hpp(160): fatal error in "cl_int boost::compute::program::build(const string&)": std::exception: Invalid Operation  
/home/demidov/work/opencl/compute/test/test_scan.cpp(36): last checkpoint  
Boost.Compute: kernel compilation failed (-59)  
--- source ---  
  
__kernel void copy(__global int* _buf0)  
{  
const uint i = get_global_id(0);  
_buf0[i]=0;  
  
}  
  
--- build log ---  
  
/home/demidov/work/opencl/compute/include/boost/compute/program.hpp(160): fatal error in "cl_int boost::compute::program::build(const string&)": std::exception: Invalid Operation  
/home/demidov/work/opencl/compute/test/test_scan.cpp(71): last checkpoint  
Boost.Compute: kernel compilation failed (-59)  
--- source ---  
  
__kernel void copy(__global int2* _buf0)  
{  
const uint i = get_global_id(0);  
_buf0[i]=0;  
  
}  
  
--- build log ---  
  
/home/demidov/work/opencl/compute/include/boost/compute/program.hpp(160): fatal error in "cl_int boost::compute::program::build(const string&)": std::exception: Invalid Operation  
/home/demidov/work/opencl/compute/test/test_scan.cpp(108): last checkpoint  
Boost.Compute: kernel compilation failed (-59)  
--- source ---  
  
__kernel void copy(__global int* _buf0)  
{  
const uint i = get_global_id(0);  
_buf0[i]=0;  
  
}  
  
--- build log ---  
  
/home/demidov/work/opencl/compute/include/boost/compute/program.hpp(160): fatal error in "cl_int boost::compute::program::build(const string&)": std::exception: Invalid Operation  
/home/demidov/work/opencl/compute/test/test_scan.cpp(108): last checkpoint  
Boost.Compute: kernel compilation failed (-59)  
--- source ---  
  
__kernel void copy(__global int* _buf0)  
{  
const uint i = get_global_id(0);  
_buf0[i]=0;  
  
}  
  
--- build log ---  
  
/home/demidov/work/opencl/compute/include/boost/compute/program.hpp(160): fatal error in "cl_int boost::compute::program::build(const string&)": std::exception: Invalid Operation  
/home/demidov/work/opencl/compute/test/test_scan.cpp(108): last checkpoint  
Boost.Compute: kernel compilation failed (-59)  
--- source ---  
  
__kernel void copy(__global float* _buf0)  
{  
const uint i = get_global_id(0);  
_buf0[i]=0;  
  
}  
  
--- build log ---  
  
/home/demidov/work/opencl/compute/include/boost/compute/program.hpp(160): fatal error in "cl_int boost::compute::program::build(const string&)": std::exception: Invalid Operation  
/home/demidov/work/opencl/compute/test/test_scan.cpp(108): last checkpoint  
  
*** 6 failures detected in test suite "TestScan"  
  
```

---

## Comment 6

> Username: ddemidov  
> Created at: 2013-03-20 11:53:53 UTC  
> Url: https://github.com/boostorg/compute/issues/1#issuecomment-15171000  

VexCL/Boost.Compute interaction works as intended now. The problem was apparently in premature destruction of some temporary objects passed by reference. It seems that one has to keep in scope cl::Buffer objects that are used for creation of compute::buffer_iterators.  
  
Some working examples of the interaction may be found here:  
https://github.com/ddemidov/vexcl/tree/master/examples/boost.compute

---

## Comment 7

> Username: ddemidov  
> Created at: 2013-03-20 17:59:24 UTC  
> Url: https://github.com/boostorg/compute/issues/1#issuecomment-15192602  

[This line](https://github.com/kylelutz/compute/blob/master/include/boost/compute/iterator/buffer_iterator.hpp#L114) is probably the reason for the problems we had. [Here](https://github.com/ddemidov/vexcl/blob/ccc57ffb21dc70a4d27b9fedcae13738627c136f/vexcl/external/boost_compute.hpp#L15) temporary compute::buffer object was created, its address was stored in buffer_iterator, and the object was immediately destroyed.

---

## Comment 8

> Username: kylelutz  
> Created at: 2013-03-20 22:23:55 UTC  
> Url: https://github.com/boostorg/compute/issues/1#issuecomment-15207632  

Hmm. I'll have to think about the semantics a little more. To me it makes sense that the buffer object should persist outside the iterator.   
  
We could move to having the iterator make a copy of the buffer object at the expense of a couple clRetainMemObject/clReleaseMemObject calls. Let me know what you think.

---

## Comment 9

> Username: ddemidov  
> Created at: 2013-03-21 04:21:25 UTC  
> Url: https://github.com/boostorg/compute/issues/1#issuecomment-15218894  

That of course makes perfect sense. But I used compute::buffer only as a wrapper for cl_mem variable. cl_mem was persistent, while compute::buffer was not.  
  
I think cost of calls to clRetainMemObject/clReleaseMemObject would be negligible w.r.t. the kernel launch etc. May be there is even no need in reference counting, because iterators should not own their buffers.  
  
The other option would be to directly store cl_mem variables inside iterators, but that would require some additional work for implementing such methods as `get_buffer()` and `dereference()`.  
  
Btw, it seems you duplicate a lot of functionality from standard Khronos C++ bindings. Why did not you use those as a basis for Boost.Compute? That would allow for a more direct interoperation with other libraries.
