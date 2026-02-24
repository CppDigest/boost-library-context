# #83 - compute::sort fails with as little as 33 items [Closed]

> Username: hansbogert  
> Created at: 2014-04-17 11:22:42 UTC  
> Updated at: 2014-09-21 12:15:03 UTC  
> Closed at: 2014-09-21 12:15:03 UTC  
> Url: https://github.com/boostorg/compute/issues/83  

using https://github.com/kylelutz/compute/blob/master/example/sort_vector.cpp  
we can make it fail by using a host_vector initialized with more than 32 items.  
Tested on mac osx with a hd4000 and on linux using a nvidia card.  
  
On mac osx:  
  
``` ./sort_vector.osx  
input: [ 7, 49, 73, 58, 30, 72, 44, 78, 23, 9, 40, 65, 92, 42, 87, 3, 27, 29, 40, 12, 3, 69, 9, 57, 60, 33, 99, 78, 16, 35, 97, 26, 12 ]  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::context_error> >'  
  what():  [CL_DEVICE_NOT_AVAILABLE] : OpenCL Error : Error: Build Program driver returned (10015)  
[1]    92979 abort      ./sort_vector.osx  
```  
  
On linux/nvidia:  
  
``` ./sort_vector.linux  
input: [ 83, 86, 77, 15, 93, 35, 86, 92, 49, 21, 62, 27, 90, 59, 63, 26, 40, 26, 72, 36, 11, 68, 67, 29, 82, 30, 62, 23, 67, 35, 29, 2, 22 ]  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::runtime_exception> >'  
  what():  Build Program Failure  
Stack dump:  
[1]    19134 segmentation fault (core dumped)  ./sort_vector.linux  
```  
  
Easy c&p reproduce:  
https://gist.github.com/hansbogert/10975461

---

## Comment 1

> Username: ddemidov  
> Created at: 2014-04-17 11:40:02 UTC  
> Updated at: 2014-04-17 11:40:35 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40705260  

The example works on my machine with both Intel and NVIDIA SDKs:  
  
```  
./sort   
input: [ 83, 86, 77, 15, 93, 35, 86, 92, 49, 21, 62, 27, 90, 59, 63, 26, 40, 26, 72, 36, 11, 68, 67, 29, 82, 30, 62, 23, 67, 35, 29, 2, 22 ]  
output: [ 2, 11, 15, 21, 22, 23, 26, 26, 27, 29, 29, 30, 35, 35, 36, 40, 49, 59, 62, 62, 63, 67, 67, 68, 72, 77, 82, 83, 86, 86, 90, 92, 93 ]  
```  
  
`compute::sort` also works for me with much larger inputs (a couple of millions). It seems that your version of OpenCL has problems with kernels compilation. Can you successfully run other examples from Boost.Compute or even something as simple as https://gist.github.com/ddemidov/2925717?

---

## Comment 2

> Username: hansbogert  
> Created at: 2014-04-17 11:55:45 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40706256  

Yes I can run other examples e.g. changing the input to 32 items does not fail, so the problem is somewhere in radix_sort.  
I can't try your example because 1) it is opencl 1.2 (correct me if I'm wrong) and I don't have nvidia opencl1.2 headers i.e. CL/cl.hpp. and 2) it does not compile on OSX.

---

## Comment 3

> Username: hansbogert  
> Created at: 2014-04-17 12:07:50 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40707023  

My mistake, I needed cl.h **pp**  
  
I can run it, but both my setups do not contain double precision capable hardware:  
  
```  
$ ./hello  
GPUs with double precision not found.  
```

---

## Comment 4

> Username: ddemidov  
> Created at: 2014-04-17 12:11:52 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40707291  

It should be enough to delete lines [10-16](https://gist.github.com/ddemidov/2925717#file-hello-cpp-L10-L16) and replace `double` with `float` throughout the example. Sorry for inconvenience.

---

## Comment 5

> Username: ddemidov  
> Created at: 2014-04-17 12:12:48 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40707353  

Lines [55-60](https://gist.github.com/ddemidov/2925717#file-hello-cpp-L55-L60) should also be removed.

---

## Comment 6

> Username: hansbogert  
> Created at: 2014-04-17 12:17:48 UTC  
> Updated at: 2014-04-17 12:18:02 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40707668  

as expected, does not fail:  
  
```  
./hello  
GeForce 9600 GT  
3  
```

---

## Comment 7

> Username: ddemidov  
> Created at: 2014-04-17 12:24:13 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40708143  

9600 GT is compute capability 1.0, which does not suport atomic operations. And the kernels generated for the example do use atomics.

---

## Comment 8

> Username: ddemidov  
> Created at: 2014-04-17 12:30:23 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40708588  

And the error message for hd4000 says `device not available`. Are you able to run the example there?

---

## Comment 9

> Username: hansbogert  
> Created at: 2014-04-17 12:33:03 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40708800  

Yes,   
  
```  
./hello.osx  
HD Graphics 4000  
3  
```

---

## Comment 10

> Username: roshanrags  
> Created at: 2014-04-17 22:30:43 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40770245  

I am able to reproduce this as well... The problem is with `radix_sort`. The `CL_DEVICE_NOT_AVAILABLE` error usually occurs when the program source for the kernel fails to compile.

---

## Comment 11

> Username: kylelutz  
> Created at: 2014-04-18 01:35:19 UTC  
> Updated at: 2014-04-18 01:35:55 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40780052  

Ya, like Denis said, the `radix_sort()` uses atomics which are not supported by your hardware. The reason you see a difference when running different numbers of values is that internally `sort()` will use and insertion sort algorithm (which doesn't require atomics) for tiny inputs (32 or less values) and the radix sort algorithm for anything larger.

---

## Comment 12

> Username: hansbogert  
> Created at: 2014-04-18 08:57:56 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40796310  

Just to verify, my nvidia 9600gt as **well as the intel hd4000** do not have support for atomics?

---

## Comment 13

> Username: ddemidov  
> Created at: 2014-04-18 09:14:40 UTC  
> Updated at: 2014-04-18 09:15:23 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40797168  

Can you compile and run the following code? It should list your devices with supported extensions. If the device supports atomics, it should have `cl_khr_local_int32_base_atomics`, `cl_khr_local_int32_extended_atomics` in the output.  
  
``` .cpp  
#include <iostream>  
#include <vector>  
#include <string>  
  
#define __CL_ENABLE_EXCEPTIONS  
#include <CL/cl.hpp>  
  
int main() {  
    try {  
        // Get list of OpenCL platforms.  
        std::vector<cl::Platform> platform;  
        cl::Platform::get(&platform);  
  
        if (platform.empty()) {  
            std::cerr << "OpenCL platforms not found." << std::endl;  
            return 1;  
        }  
  
        for(auto p = platform.begin(); p != platform.end(); p++) {  
            std::vector<cl::Device> pldev;  
  
            try {  
                p->getDevices(CL_DEVICE_TYPE_ALL, &pldev);  
  
                for(auto d = pldev.begin(); d != pldev.end(); d++) {  
                    std::cout << d->getInfo<CL_DEVICE_NAME>() << ":\n\t"  
                        << d->getInfo<CL_DEVICE_EXTENSIONS>() << std::endl;  
                }  
            } catch(...) {  
            }  
        }  
    } catch (const cl::Error &err) {  
        std::cerr  
            << "OpenCL error: "  
            << err.what() << "(" << err.err() << ")"  
            << std::endl;  
        return 1;  
    }  
}  
```

---

## Comment 14

> Username: hansbogert  
> Created at: 2014-04-18 09:40:17 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40798372  

Seems it's supported   
  
```  
Intel(R) Core(TM) i5-3427U CPU @ 1.80GHz:  
    cl_APPLE_SetMemObjectDestructor cl_APPLE_ContextLoggingFunctions cl_APPLE_clut cl_APPLE_query_kernel_names cl_APPLE_gl_sharing cl_khr_gl_event cl_khr_fp64 cl_khr_global_int32_base_atomics cl_khr_global_int32_extended_atomics cl_khr_local_int32_base_atomics cl_khr_local_int32_extended_atomics cl_khr_byte_addressable_store cl_khr_int64_base_atomics cl_khr_int64_extended_atomics cl_khr_3d_image_writes cl_khr_image2d_from_buffer cl_APPLE_fp64_basic_ops cl_APPLE_fixed_alpha_channel_orders cl_APPLE_biased_fixed_point_image_formats cl_APPLE_command_queue_priority  
HD Graphics 4000:  
    cl_APPLE_SetMemObjectDestructor cl_APPLE_ContextLoggingFunctions cl_APPLE_clut cl_APPLE_query_kernel_names cl_APPLE_gl_sharing cl_khr_gl_event cl_khr_global_int32_base_atomics cl_khr_global_int32_extended_atomics cl_khr_local_int32_base_atomics cl_khr_local_int32_extended_atomics cl_khr_byte_addressable_store cl_khr_image2d_from_buffer cl_khr_gl_depth_images cl_khr_depth_images  
```

---

## Comment 15

> Username: kylelutz  
> Created at: 2014-04-18 15:24:49 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-40816881  

Those should work. Can you add `#define BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION` to the top of your file (before any `<boost/compute/*>` includes. This will then print out information indicating why the kernel failed to build.

---

## Comment 16

> Username: hansbogert  
> Created at: 2014-04-22 06:38:55 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-41008202  

added the line to the sort_vector example:  
  
```  
//---------------------------------------------------------------------------//  
// Copyright (c) 2013 Kyle Lutz <kyle.r.lutz@gmail.com>  
//  
// Distributed under the Boost Software License, Version 1.0  
// See accompanying file LICENSE_1_0.txt or copy at  
// http://www.boost.org/LICENSE_1_0.txt  
//  
// See http://kylelutz.github.com/compute for more information.  
//---------------------------------------------------------------------------//  
  
#include <algorithm>  
#include <iostream>  
#include <vector>  
  
#define BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION  
#include <boost/compute/system.hpp>  
#include <boost/compute/algorithm/copy.hpp>  
#include <boost/compute/algorithm/sort.hpp>  
#include <boost/compute/container/vector.hpp>  
...  
```  
  
Output is the same as without the `#define`  
  
```  
./sort_vector.osx  
input: [ 7, 49, 73, 58, 30, 72, 44, 78, 23, 9, 40, 65, 92, 42, 87, 3, 27, 29, 40, 12, 3, 69, 9, 57, 60, 33, 99, 78, 16, 35, 97, 26, 12 ]  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::context_error> >'  
  what():  [CL_DEVICE_NOT_AVAILABLE] : OpenCL Error : Error: Build Program driver returned (10015)  
[1]    19241 abort      ./sort_vector.osx  
```

---

## Comment 17

> Username: kylelutz  
> Created at: 2014-04-23 01:38:15 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-41116092  

That's strange. Can you add the following two lines to the top of main:  
  
```  
const boost::compute::device device = boost::compute::system::default_device();  
std::cout << "device: " << device.name() << std::endl;  
```  
  
This should find the default OpenCL device (or any OpenCL device) and print its name to stdout. If that doesn't work then there is something wrong with the OpenCL implementation installed on your system. Ensure that the correct OpenCL library/framework is being linked.

---

## Comment 18

> Username: hansbogert  
> Created at: 2014-04-23 06:46:42 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-41129785  

printing the device works:  
  
```  
./sort_vector.osx  
device: HD Graphics 4000  
input: [ 7, 49, 73, 58, 30, 72, 44, 78, 23, 9, 40, 65, 92, 42, 87, 3, 27, 29, 40, 12, 3, 69, 9, 57, 60, 33, 99, 78, 16, 35, 97, 26, 12 ]  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::context_error> >'  
  what():  [CL_DEVICE_NOT_AVAILABLE] : OpenCL Error : Error: Build Program driver returned (10015)  
[1]    42740 abort      ./sort_vector.osx  
```

---

## Comment 19

> Username: kylelutz  
> Created at: 2014-04-24 05:17:41 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-41243545  

This is very strange. I found this post on the Intel forums (https://software.intel.com/en-us/forums/topic/505677) but with no resolution (and the error message from the OpenCL implementation isn't very helpful).

---

## Comment 20

> Username: hansbogert  
> Created at: 2014-04-24 11:39:12 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-41270167  

Commented on the intel forum to ask the topic starter if there's any followup

---

## Comment 21

> Username: roshanrags  
> Created at: 2014-09-20 06:51:40 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-56258904  

@hansbogert   
I'm no longer able to reproduce this(tried with 33 and 50 items) both on my Intel HD 4000 as well as Geforce 650M. Perhaps an OS update fixed it(running Yosemite DP 8 now). Check if it is fixed for you as well.

---

## Comment 22

> Username: hansbogert  
> Created at: 2014-09-21 12:15:03 UTC  
> Url: https://github.com/boostorg/compute/issues/83#issuecomment-56297000  

Indeed solved, bisected it and came down to commit: a78212fdde9254c18cabacd4388ca7106dbbcdbd  
  
```  
Rename K to K_BITS in radix_sort()  
  
    This should fix the following error seen on the Apple OpenCL  
    implementation when compiling the radix_sort program: "error:  
    definition of macro 'K' conflicts with an identifier used in  
    the precompiled header".  
```  
  
So solved long ago.
