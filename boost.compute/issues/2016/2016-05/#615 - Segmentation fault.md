# #615 - [OS X 10.11.5] [GeForce GT 750M] Segmentation	fault [Open]

> Username: prthakre  
> Created at: 2016-05-24 05:09:37 UTC  
> Updated at: 2017-04-22 13:45:59 UTC  
> Url: https://github.com/boostorg/compute/issues/615  

Hi,  
   Am trying to sort a vector on GPU (GeForce GT 750M) using example code which generates  SEGV.  
   However, it runs fine on Iris Pro (Intel(R) Core(TM) i7-4850HQ CPU).  
  
   We observe SEGV when size is 10 million.  
   When the size is 5 million, following exception is thrown:   
boost/1_61_0/include/boost/compute/command_queue.hpp(453): Throw in function  
 boost::compute::event boost::compute::command_queue::enqueue_write_buffer(const   
 boost::compute::buffer &, size_t, size_t, const void *, const boost::compute::wait_list  
 &) Dynamic exception type:   
 boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<  
   boost::compute::opencl_error> > std::exception::what: Invalid Value  
  
```  
Another observation: if the size of vector is 50 million the sorting works fine,   
```  
  
though the timings are worse than Iris Pro.  
    Also, when is size is 100 million, the binary causes the OS to crash.  
  
   Compiler details:  
    clang++ --version  
    Apple LLVM version 7.3.0 (clang-703.0.31)  
    Target: x86_64-apple-darwin15.5.0  
    Thread model: posix  
  
   OS:  
    System Version: OS X 10.11.5 (15F34)  
    Kernel Version: Darwin 15.5.0  
Regards,  
Prashant  
  
----------------------------Cut here-------------------------------------  
  
```  
#include <iostream>  
#include <vector>  
#include <algorithm>  
#include <boost/foreach.hpp>  
#include <boost/compute/core.hpp>  
#include <boost/compute/platform.hpp>  
#include <boost/compute/algorithm.hpp>  
#include <boost/compute/container/vector.hpp>  
#include <boost/compute/functional/math.hpp>  
#include <boost/compute/types/builtin.hpp>  
#include <boost/compute/function.hpp>  
#include <boost/chrono/include.hpp>  
#include <boost/exception/all.hpp>  
  
namespace compute = boost::compute;  
  
int main(int argc, char* argv[])  
{  
    if (argc != 2) {  
        std::cout << "Usage: " << argv[0] << " <size> " << std::endl;  
        return 0;  
    }  
  
    // generate random data on the host  
    std::vector<float> host_vector(atoi(argv[1]));  
    std::generate(host_vector.begin(), host_vector.end(), rand);  
  
    std::cout << "===============CPU==================\n";  
    for (size_t k=0; k<5; k++)  
    {  
        std::vector<float> host_copy_vector(host_vector);  
        auto start = std::chrono::high_resolution_clock::now();  
        std::sort(host_copy_vector.begin(), host_copy_vector.end());  
  
        auto duration = std::chrono::duration_cast<std::chrono::milliseconds>  
(std::chrono::high_resolution_clock::now() - start);  
        std::cout << "time: iteration ("<< k << ") : " << duration.count() << " ms" <<   
std::endl;  
    }  
  
    std::vector<compute::platform> platforms = compute::system::platforms();  
  
    for(size_t i = 0; i < platforms.size(); i++){  
        const compute::platform &platform = platforms[i];  
        std::cout << "Platform '" << platform.name() << "'" << std::endl;  
        std::vector<compute::device> devices = platform.devices();  
  
        for(size_t j = 0; j < devices.size(); j++){  
            const compute::device &device = devices[j];  
  
            std::string type;  
            if(device.type() & compute::device::gpu)  
                type = "GPU Device";  
            else if(device.type() & compute::device::cpu)  
                type = "CPU Device";  
            else if(device.type() & compute::device::accelerator)  
                type = "Accelerator Device";  
            else  
                type = "Unknown Device";  
  
            if (type != "GPU Device") {  
                std::cout << "Ignoring non GPU devices.\n";  
                continue;  
            }  
  
            std::cout << "====\n";  
            std::cout << "  " << type << ": " << device.name() << std::endl;  
            std::cout << "====\n";  
            compute::context context(device);  
            compute::command_queue queue(context, device);  
  
            for (size_t k=0; k<5; k++)  
            {  
                compute::vector<float> device_vector(host_vector.size(), context);  
  
                // copy data from the host to the device  
                compute::copy(  
                    host_vector.begin(), host_vector.end(), device_vector.begin(), queue  
                );  
  
                auto start = std::chrono::high_resolution_clock::now();  
                try {  
                  compute::sort(device_vector.begin(), device_vector.end(), queue);  
                } catch (boost::exception & e) {  
                  std::cerr << diagnostic_information(e);  
                  break;  
                }  
  
                auto duration = std::chrono::duration_cast<std::chrono::milliseconds>  
(std::chrono::high_resolution_clock::now() - start);  
                std::cout << "time: iteration ("<< k << ") : " << duration.count() << " ms" <<  
std::endl;  
            }  
  
            std::cout <<  "====\n";  
        }  
    }  
  
    return 0;  
}  
```  
  
----------------------------Cut here-------------------------------------  
##

---

## Comment 1

> Username: pavanky  
> Created at: 2016-05-24 06:55:25 UTC  
> Updated at: 2016-05-24 10:20:21 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221182230  

Hmm. We are seeing a [similar problem happen in arrayfire](https://github.com/arrayfire/arrayfire/issues/1054) independent of boost compute. Perhaps the underlying issue is the same. We haven't been able to narrow this down yet.

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-05-24 06:55:44 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221182273  

It looks like this  
  
``` cpp  
compute::copy(  
  host_vector.begin(), host_vector.end(), device_vector.begin(), queue  
);  
```  
  
fails.

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-05-24 06:58:47 UTC  
> Updated at: 2016-05-24 07:03:44 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221182781  

Can you provide a backtrace from gdb? It would also be great to have output from `clinfo` program. I doubt that there is a `clinfo` on OSX, but there are plenty of projects like this on Github (for example https://github.com/Oblomov/clinfo).

---

## Comment 4

> Username: prthakre  
> Created at: 2016-05-24 10:05:31 UTC  
> Updated at: 2016-05-25 05:16:51 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221224010  

Please find below the output from lldb:  
  
```  
(lldb) process launch -- 10000000  
Process 14431 launched: './new_bench' (x86_64)  
===============================CPU=================================  
time: iteration (1) : 849 ms  
time: iteration (2) : 856 ms  
time: iteration (3) : 835 ms  
time: iteration (4) : 853 ms  
time: iteration (5) : 832 ms  
====================================================================  
Platform 'Apple'  
====================================================================  
  CPU Device: Intel(R) Core(TM) i7-4850HQ CPU @ 2.30GHz  
====================================================================  
time: iteration (1) : 291 ms  
time: iteration (2) : 231 ms  
time: iteration (3) : 232 ms  
time: iteration (4) : 224 ms  
time: iteration (5) : 239 ms  
====================================================================  
====================================================================  
  GPU Device: Iris Pro  
====================================================================  
time: iteration (1) : 362 ms  
time: iteration (2) : 335 ms  
time: iteration (3) : 336 ms  
time: iteration (4) : 336 ms  
time: iteration (5) : 336 ms  
====================================================================  
====================================================================  
  GPU Device: GeForce GT 750M  
====================================================================  
Process 14431 stopped  
* thread #9: tid = 0x55164, 0x00007fff9de4e2d6 libclh.dylib`cuiModuleUnloadEx + 102, queue = 'opencl_runtime', stop reason = EXC_BAD_ACCESS (code=1, address=0x10)  
    frame #0: 0x00007fff9de4e2d6 libclh.dylib`cuiModuleUnloadEx + 102  
libclh.dylib`cuiModuleUnloadEx:  
->  0x7fff9de4e2d6 <+102>: movq   0x10(%r15), %rdi  
    0x7fff9de4e2da <+106>: addq   $0x260, %rdi              ; imm = 0x260   
    0x7fff9de4e2e1 <+113>: xorl   %esi, %esi  
    0x7fff9de4e2e3 <+115>: xorl   %edx, %edx  
(lldb) bt  
* thread #9: tid = 0x55164, 0x00007fff9de4e2d6 libclh.dylib`cuiModuleUnloadEx + 102, queue = 'opencl_runtime', stop reason = EXC_BAD_ACCESS (code=1, address=0x10)  
  * frame #0: 0x00007fff9de4e2d6 libclh.dylib`cuiModuleUnloadEx + 102  
    frame #1: 0x00007fff9de4cf2e libclh.dylib`cuiModuleUnload + 14  
    frame #2: 0x00007fff8b9373dd GeForceGLDriver`___lldb_unnamed_function11029$$GeForceGLDriver + 461  
    frame #3: 0x00007fff8b934fb7 GeForceGLDriver`___lldb_unnamed_function11020$$GeForceGLDriver + 452  
    frame #4: 0x00007fff8b934ce1 GeForceGLDriver`gldCopyBufferDataWithQueue + 1017  
    frame #5: 0x00007fff994443f8 OpenCL`___lldb_unnamed_function18$$OpenCL + 862  
    frame #6: 0x00007fff99462529 OpenCL`___lldb_unnamed_function442$$OpenCL + 101  
    frame #7: 0x00007fff994659e0 OpenCL`___lldb_unnamed_function475$$OpenCL + 326  
    frame #8: 0x00007fff8e27840b libdispatch.dylib`_dispatch_client_callout + 8  
    frame #9: 0x00007fff8e27d03b libdispatch.dylib`_dispatch_queue_drain + 754  
    frame #10: 0x00007fff8e283707 libdispatch.dylib`_dispatch_queue_invoke + 549  
    frame #11: 0x00007fff8e27840b libdispatch.dylib`_dispatch_client_callout + 8  
    frame #12: 0x00007fff8e27c29b libdispatch.dylib`_dispatch_root_queue_drain + 1890  
    frame #13: 0x00007fff8e27bb00 libdispatch.dylib`_dispatch_worker_thread3 + 91  
    frame #14: 0x00007fff917994de libsystem_pthread.dylib`_pthread_wqthread + 1129  
    frame #15: 0x00007fff91797341 libsystem_pthread.dylib`start_wqthread + 13  
(lldb)   
```

---

## Comment 5

> Username: prthakre  
> Created at: 2016-05-24 10:11:55 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221225505  

Output of clinfo  
[cl_info.txt](https://github.com/boostorg/compute/files/279236/cl_info.txt)

---

## Comment 6

> Username: jszuppe  
> Created at: 2016-05-24 10:21:37 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221227574  

I was hoping for backtrace that will confirm that exception:  
  
``` bash  
boost/1_61_0/include/boost/compute/command_queue.hpp(453): Throw in function  
boost::compute::event boost::compute::command_queue::enqueue_write_buffer(const   
boost::compute::buffer &, size_t, size_t, const void *, const boost::compute::wait_list  
&) Dynamic exception type:   
boost::exception_detail::clone_impl boost::compute::opencl_error> > std::exception::what: Invalid Value  
```  
  
was thrown by   
  
``` cpp  
compute::copy(  
  host_vector.begin(), host_vector.end(), device_vector.begin(), queue  
);  
```  
  
Anyway, I don't think `boost::compute::command_queue::enqueue_write_buffer()` may be anywhere else. In `sort()` we may initialize some temporary buffers, but I think we do not copy anything from host to any of those buffers. Does it [failure] still happen if you change `sort()` to `transform()` (or any other algorithm)?

---

## Comment 7

> Username: prthakre  
> Created at: 2016-05-24 11:29:26 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221241318  

I will try to get the backtrace for exception scenario.  
Having said that, if you look at timings, CPU Device through boost::compute is fastest.  
Is this expected ?

---

## Comment 8

> Username: jszuppe  
> Created at: 2016-05-24 11:35:54 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221242515  

Yes, imho it should be that way since Boost.Compute implementation uses all  
CPU cores. Iris isn't so fast.  
24 maj 2016 13:29 "prthakre" notifications@github.com napisał(a):  
  
> I will try to get the backtrace for exception scenario.  
> Having said that, if you look at timings, CPU Device through  
> boost::compute is fastest.  
> Is this expected ?  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/issues/615#issuecomment-221241318

---

## Comment 9

> Username: pavanky  
> Created at: 2016-05-24 11:40:26 UTC  
> Updated at: 2016-05-24 11:40:41 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221243356  

>  Iris isn't so fast.  
  
@haahh that's not the case. Intel GPUs require vector data types to perform better.

---

## Comment 10

> Username: jszuppe  
> Created at: 2016-05-24 12:04:16 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221248044  

Yes, indeed, it may be the problem. Do you know any performance comparisons  
between intel cpu (all cores) and iris?  
24 maj 2016 13:40 "Pavan Yalamanchili" notifications@github.com  
napisał(a):  
  
> Iris isn't so fast.  
> @haahh https://github.com/haahh that's not the case. Intel GPUs require  
> vector data types to perform better.  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/issues/615#issuecomment-221243356

---

## Comment 11

> Username: prthakre  
> Created at: 2016-05-24 12:17:26 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221250811  

Hopefully this is the trace you were looking for:  
  
#   GPU Device: GeForce GT 750M  
  
Process 14862 stopped  
- thread #12: tid = 0x615e4, 0x00007fff9de4e2d6 libclh.dylib`cuiModuleUnloadEx + 102, queue = 'opencl_runtime', stop reason = EXC_BAD_ACCESS (code=1, address=0x10)  
  frame #0: 0x00007fff9de4e2d6 libclh.dylib`cuiModuleUnloadEx + 102  
  libclh.dylib`cuiModuleUnloadEx:  
  ->  0x7fff9de4e2d6 <+102>: movq   0x10(%r15), %rdi  
  0x7fff9de4e2da <+106>: addq   $0x260, %rdi              ; imm = 0x260   
  0x7fff9de4e2e1 <+113>: xorl   %esi, %esi  
  0x7fff9de4e2e3 <+115>: xorl   %edx, %edx  
  (lldb) bt  
- thread #12: tid = 0x615e4, 0x00007fff9de4e2d6 libclh.dylib`cuiModuleUnloadEx + 102, queue = 'opencl_runtime', stop reason = EXC_BAD_ACCESS (code=1, address=0x10)  
  - frame #0: 0x00007fff9de4e2d6 libclh.dylib`cuiModuleUnloadEx + 102  
    frame #1: 0x00007fff9de4cf2e libclh.dylib`cuiModuleUnload + 14  
    frame #2: 0x00007fff8b9373dd GeForceGLDriver`___lldb_unnamed_function11029$$GeForceGLDriver + 461  
    frame #3: 0x00007fff8b934fb7 GeForceGLDriver`___lldb_unnamed_function11020$$GeForceGLDriver + 452  
    frame #4: 0x00007fff8b934ce1 GeForceGLDriver`gldCopyBufferDataWithQueue + 1017  
    frame #5: 0x00007fff994443f8 OpenCL`___lldb_unnamed_function18$$OpenCL + 862  
    frame #6: 0x00007fff99462529 OpenCL`___lldb_unnamed_function442$$OpenCL + 101  
    frame #7: 0x00007fff994659e0 OpenCL`___lldb_unnamed_function475$$OpenCL + 326  
    frame #8: 0x00007fff8e27840b libdispatch.dylib`_dispatch_client_callout + 8  
    frame #9: 0x00007fff8e27d03b libdispatch.dylib`_dispatch_queue_drain + 754  
    frame #10: 0x00007fff8e283707 libdispatch.dylib`_dispatch_queue_invoke + 549  
    frame #11: 0x00007fff8e27840b libdispatch.dylib`_dispatch_client_callout + 8  
    frame #12: 0x00007fff8e27c29b libdispatch.dylib`_dispatch_root_queue_drain + 1890  
    frame #13: 0x00007fff8e27bb00 libdispatch.dylib`_dispatch_worker_thread3 + 91  
    frame #14: 0x00007fff917994de libsystem_pthread.dylib`_pthread_wqthread + 1129  
    frame #15: 0x00007fff91797341 libsystem_pthread.dylib`start_wqthread + 13

---

## Comment 12

> Username: prthakre  
> Created at: 2016-05-24 12:23:46 UTC  
> Updated at: 2016-05-25 05:17:58 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221252215  

I replaced  compute::sort(device_vector.begin(), device_vector.end(), queue); with  
                  compute::transform(  
                    device_vector.begin(),  
                    device_vector.end(),  
                    device_vector.begin(),  
                    compute::sqrt<float>(),  
                    queue  
                  );  
And, there is no SEGV.  
However, there seems to be a problem with the vector holding computed values.  
In case of Iris Pro: the vector holding sqrt values is != to others. Am hoping this is because of float values, but not sure why is this specific to Iris Pro.  
  
Here is the output:  
  
```  
==============================CPU=================================  
time: iteration (1) : 82 ms  
time: iteration (2) : 82 ms  
time: iteration (3) : 78 ms  
time: iteration (4) : 77 ms  
time: iteration (5) : 78 ms  
====================================================================  
  
====================================================================  
  CPU Device: Intel(R) Core(TM) i7-4850HQ CPU @ 2.30GHz  
====================================================================  
time: iteration (1) : 6 ms  Equals   
time: iteration (2) : 0 ms  Equals   
time: iteration (3) : 0 ms  Equals   
time: iteration (4) : 0 ms  Equals   
time: iteration (5) : 0 ms  Equals   
====================================================================  
====================================================================  
  GPU Device: Iris Pro  
====================================================================  
time: iteration (1) : 1 ms  Incorrect sqrt   
time: iteration (2) : 0 ms  Incorrect sqrt   
time: iteration (3) : 0 ms  Incorrect sqrt   
time: iteration (4) : 1 ms  Incorrect sqrt   
time: iteration (5) : 0 ms  Incorrect sqrt   
====================================================================  
====================================================================  
  GPU Device: GeForce GT 750M  
====================================================================  
time: iteration (1) : 2 ms  Equals   
time: iteration (2) : 0 ms  Equals   
time: iteration (3) : 0 ms  Equals   
time: iteration (4) : 0 ms  Equals   
time: iteration (5) : 0 ms  Equals   
====================================================================  
```

---

## Comment 13

> Username: pavanky  
> Created at: 2016-05-24 21:16:46 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221403957  

> Yes, indeed, it may be the problem. Do you know any performance comparisons  
> between intel cpu (all cores) and iris?  
  
Unfortunately arrayfire faces the same performance issues because Intel GPUs are the oddball on the desktop. We plan to do some testing at sometime, but right now not worth the time.

---

## Comment 14

> Username: kylelutz  
> Created at: 2016-05-25 05:36:49 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221477683  

So I can reproduce the segfault at size `10000000` and "Invalid Value" exception at size `5000000`.  
  
Here's the backtrace from the "Invalid Value" exception:  
  
```  
* thread #1: tid = 0x627b92, 0x00007fff82467ab7 libc++abi.dylib`__cxa_throw, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1  
  * frame #0: 0x00007fff82467ab7 libc++abi.dylib`__cxa_throw  
    frame #1: 0x00000001000087a1 hello_world`void boost::throw_exception<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >(e=0x00007fff5fbfb990) + 113 at throw_exception.hpp:69  
    frame #2: 0x00000001000086b5 hello_world`void boost::exception_detail::throw_exception_<boost::compute::opencl_error>(x=0x00007fff5fbfbab0, current_function="boost::compute::event boost::compute::command_queue::enqueue_write_buffer(const boost::compute::buffer &, size_t, size_t, const void *, const boost::compute::wait_list &)", file="../include/boost/compute/command_queue.hpp", line=453) + 197 at throw_exception.hpp:86  
    frame #3: 0x00000001000167e6 hello_world`boost::compute::command_queue::enqueue_write_buffer(this=0x00007fff5fbff320, buffer=0x00007fff5fbfbca8, offset=0, size=64, host_ptr=0x00007fff5fbfbbf0, events=0x00007fff5fbfbbc8) + 806 at command_queue.hpp:453  
    frame #4: 0x00000001000ab5f7 hello_world`void boost::compute::detail::dispatch_fill<boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::uint16_>(first=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfbca8, count=1, value=0x00007fff5fbfc880, queue=0x00007fff5fbff320, (null)=0x0000000000000000) + 343 at fill.hpp:127  
    frame #5: 0x00000001000ab2dd hello_world`void boost::compute::fill<boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::uint16_>(first=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfc870, last=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfc860, value=0x00007fff5fbfc880, queue=0x00007fff5fbff320) + 221 at fill.hpp:286  
    frame #6: 0x00000001000a9d4f hello_world`boost::compute::buffer_iterator<boost::compute::uint16_> boost::compute::detail::scan_impl<boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::uint16_, boost::compute::plus<boost::compute::uint16_> >(first=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfd068, last=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfd058, result=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfd048, exclusive=false, init=uint16_ @ 0x00007fff5fbfd008, op=plus<boost::compute::uint16_> @ 0x00007fff5fbfcfc0, queue=0x00007fff5fbff320) + 751 at scan_on_gpu.hpp:222  
    frame #7: 0x00000001000aa1dc hello_world`boost::compute::buffer_iterator<boost::compute::uint16_> boost::compute::detail::scan_impl<boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::uint16_, boost::compute::plus<boost::compute::uint16_> >(first=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfd940, last=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfd930, result=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfd920, exclusive=true, init=uint16_ @ 0x00007fff5fbfd8e0, op=plus<boost::compute::uint16_> @ 0x00007fff5fbfd898, queue=0x00007fff5fbff320) + 1916 at scan_on_gpu.hpp:242  
    frame #8: 0x00000001000a960e hello_world`boost::compute::buffer_iterator<boost::compute::uint16_> boost::compute::detail::dispatch_scan<boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::uint16_, boost::compute::plus<boost::compute::uint16_> >(first=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfdb40, last=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfdb30, result=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfdb10, exclusive=true, init=uint16_ @ 0x00007fff5fbfdad0, op=plus<boost::compute::uint16_> @ 0x00007fff5fbfda88, queue=0x00007fff5fbff320) + 654 at scan_on_gpu.hpp:303  
    frame #9: 0x00000001000a396f hello_world`boost::compute::buffer_iterator<boost::compute::uint16_> boost::compute::detail::scan_on_gpu<boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::uint16_, boost::compute::plus<boost::compute::uint16_> >(first=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfdc90, last=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfdc80, result=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfdc70, exclusive=true, init=uint16_ @ 0x00007fff5fbfdc30, op=plus<boost::compute::uint16_> @ 0x00007fff5fbfdbe8, queue=0x00007fff5fbff320) + 335 at scan_on_gpu.hpp:324  
    frame #10: 0x00000001000a1f8e hello_world`boost::compute::buffer_iterator<boost::compute::uint16_> boost::compute::detail::scan<boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::uint16_, boost::compute::plus<boost::compute::uint16_> >(first=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfde98, last=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfde88, result=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfde68, exclusive=true, init=uint16_ @ 0x00007fff5fbfde28, op=plus<boost::compute::uint16_> @ 0x00007fff5fbfdde0, queue=0x00007fff5fbff320) + 750 at scan.hpp:37  
    frame #11: 0x00000001000454b2 hello_world`boost::compute::buffer_iterator<boost::compute::uint16_> boost::compute::exclusive_scan<boost::compute::buffer_iterator<boost::compute::uint16_>, boost::compute::buffer_iterator<boost::compute::uint16_> >(first=<unavailable>, last=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfe258, result=buffer_iterator<boost::compute::uint16_> @ 0x00007fff5fbfe248, queue=0x00007fff5fbff320) + 194 at exclusive_scan.hpp:88  
    frame #12: 0x0000000100043b12 hello_world`void boost::compute::detail::radix_sort_impl<float, int>(first=const boost::compute::buffer_iterator<float> @ 0x00007fff5fbfed68, last=const boost::compute::buffer_iterator<float> @ 0x00007fff5fbfed58, values_first=const boost::compute::buffer_iterator<int> @ 0x00007fff5fbfed38, ascending=true, queue=0x00007fff5fbff320) + 8018 at radix_sort.hpp:374  
    frame #13: 0x0000000100018a81 hello_world`void boost::compute::detail::radix_sort<boost::compute::buffer_iterator<float> >(first=buffer_iterator<float> @ 0x00007fff5fbfedc8, last=buffer_iterator<float> @ 0x00007fff5fbfedb8, queue=0x00007fff5fbff320) + 97 at radix_sort.hpp:425  
    frame #14: 0x0000000100017854 hello_world`void boost::compute::detail::dispatch_gpu_sort<float>(first=buffer_iterator<float> @ 0x00007fff5fbfef48, last=buffer_iterator<float> @ 0x00007fff5fbfef38, (null)=less<float> @ 0x00007fff5fbfeef0, queue=0x00007fff5fbff320, (null)=0x0000000000000000) + 404 at sort.hpp:52  
    frame #15: 0x00000001000174d4 hello_world`void boost::compute::detail::dispatch_sort<boost::compute::buffer_iterator<float>, boost::compute::less<float> >(first=buffer_iterator<float> @ 0x00007fff5fbff018, last=buffer_iterator<float> @ 0x00007fff5fbff008, compare=less<float> @ 0x00007fff5fbfefb0, queue=0x00007fff5fbff320, (null)=0x0000000000000000) + 228 at sort.hpp:104  
    frame #16: 0x000000010001733e hello_world`void boost::compute::sort<boost::compute::buffer_iterator<float>, boost::compute::less<float> >(first=buffer_iterator<float> @ 0x00007fff5fbff0b8, last=buffer_iterator<float> @ 0x00007fff5fbff0a8, compare=less<float> @ 0x00007fff5fbff050, queue=0x00007fff5fbff320) + 126 at sort.hpp:172  
    frame #17: 0x0000000100005dcb hello_world`void boost::compute::sort<boost::compute::buffer_iterator<float> >(first=buffer_iterator<float> @ 0x00007fff5fbff2a8, last=buffer_iterator<float> @ 0x00007fff5fbff298, queue=0x00007fff5fbff320) + 107 at sort.hpp:183  
    frame #18: 0x0000000100003681 hello_world`main(argc=2, argv=0x00007fff5fbffa80) + 4273 at hello_world.cpp:101  
    frame #19: 0x00007fff878b95c9 libdyld.dylib`start + 1  
```  
  
Looks like we're inside `scan_on_gpu()` and calling `fill()` with a single `uint16_` vector value to zero out a temporary buffer.  
  
The segfault backtrace I get is the same as @prthakre posted before.

---

## Comment 15

> Username: prthakre  
> Created at: 2016-05-25 07:17:43 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221492637  

Thanks for the confirmation.  
Do you have a quick and dirty fix for me to try out ?

---

## Comment 16

> Username: jszuppe  
> Created at: 2016-05-25 08:31:10 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-221507561  

Right now I don't know why this exception is raised. IIRC, invalid value  
error happens when memory defined by size and offset is out of bounds or  
host pointer is a null pointer.  
  
Now I can not find where is the problem. I have a busy day today, I'll try  
to locate the problem tomorrow. You can try to force filling with fill  
buffer operation by commenting out if statement in dispatch_fill in  
fill.hpp.  
  
Thanks for the confirmation.  
Do you have a quick and dirty fix for me to try out ?  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly or view it on GitHub  
https://github.com/boostorg/compute/issues/615#issuecomment-221492637

---

## Comment 17

> Username: prthakre  
> Created at: 2016-05-27 05:10:31 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-222061508  

You are asking me to comment below if block and force else to execute ?  
  
  if(count == 1){  
        // use clEnqueueWriteBuffer() directly when writing a single value  
        // to the device buffer. this is potentially more efficient and also  
        // works around a bug in the intel opencl driver.  
        queue.enqueue_write_buffer(  
            first.get_buffer(),  
            offset \* sizeof(value_type),  
            sizeof(value_type),  
            &pattern  
        );  
    }  
    else {  
        queue.enqueue_fill_buffer(  
            first.get_buffer(),  
            &pattern,  
            sizeof(value_type),  
            offset \* sizeof(value_type),  
            count \* sizeof(value_type)  
        );  
    }  
  
 Afraid, this change causes a crash irrespective of size of vector.

---

## Comment 18

> Username: little-big-h  
> Created at: 2017-02-16 04:31:21 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-280228451  

I spent about a day tracking down a problem in arrayfire to this bug. It seems to be a problem with the way the block_count is calculated in scan_impl in file scan_on_gpu.hpp. I spent some time looking at it but doesn't seem to be a straight-forward fix. Somebody should really fix this, though: it makes boost::compute essentially useless to me. I attached my minimal example.  
  
  
[BoostComputeExperiments.cpp.txt](https://github.com/boostorg/compute/files/779060/BoostComputeExperiments.cpp.txt)

---

## Comment 19

> Username: jszuppe  
> Created at: 2017-04-08 17:50:30 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-292734407  

I cannot reproduce those bugs on GTX 1080, Intel Core i7-6700K on Linux. Can anyone reproduce them (or at least one of them) on Linux or Windows with GeForce GT 750M? On a different device?  
  
>I spent about a day tracking down a problem in arrayfire to this bug. It seems to be a problem with the way the block_count is calculated in scan_impl in file scan_on_gpu.hpp.   
  
I don't see how this code:  
  
```cpp  
    const size_t count = detail::iterator_range_size(first, last);  
    size_t block_size = pick_scan_block_size(first, last);  
    size_t block_count = count / block_size;  
  
    if(block_count * block_size < count){  
        block_count++;  
    }  
```  
  
incorrectly calculate `block_count`.

---

## Comment 20

> Username: jszuppe  
> Created at: 2017-04-08 18:04:05 UTC  
> Updated at: 2017-04-09 10:40:42 UTC  
> Url: https://github.com/boostorg/compute/issues/615#issuecomment-292735260  

I'm changing issue's label to "driver bug". I'll change back to "bug" when we get information showing that those bugs occur on different device/OS, or when someone finds the error in code (I wasn't able to).
