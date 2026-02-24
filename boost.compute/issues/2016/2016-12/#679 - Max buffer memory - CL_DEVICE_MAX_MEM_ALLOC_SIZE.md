# #679 - Max buffer memory - CL_DEVICE_MAX_MEM_ALLOC_SIZE [Closed]

> Username: ppacory  
> Created at: 2016-12-30 15:28:15 UTC  
> Updated at: 2017-01-02 13:46:45 UTC  
> Closed at: 2016-12-30 18:48:33 UTC  
> Url: https://github.com/boostorg/compute/issues/679  

Platform: NVIDIA CUDA  
	Vendor:  NVIDIA Corporation  
	Version: OpenCL 1.2 CUDA 7.0.0  
  
	Number of devices: 1  
	-------------------------  
		Name: GeForce GTX TITAN X  
		Version: OpenCL C 1.2   
		Max. Compute Units: 24  
		Local Memory Size: 47 KB  
		Global Memory Size: 12287 MB  
		Max Alloc Size: 3071 MB  
		Max Work-group Size: 1024  
		Max Work-item Dims: (1024 1024 64)  
	-------------------------  
  
Here my problem,  
when i try to allocate more than 3071MB with compute::copy   
i've got this error  
  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
  what():  Memory Object Allocation Failure  
  
here a ref for the problem :   
https://devtalk.nvidia.com/default/topic/498004/opencl-6gb-memory-problem-get-error-message-at-4-2gb-of-memory/

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-12-30 16:06:43 UTC  
> Url: https://github.com/boostorg/compute/issues/679#issuecomment-269788479  

Well, it is said that max alloc size is 3071 MB, so it seems normal that you get error when you try to allocate more in one buffer.

---

## Comment 2

> Username: ppacory  
> Created at: 2016-12-30 17:30:42 UTC  
> Url: https://github.com/boostorg/compute/issues/679#issuecomment-269798704  

how to allocate more than one buffer ?  
boost::compute copy ??

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-12-30 17:43:03 UTC  
> Url: https://github.com/boostorg/compute/issues/679#issuecomment-269800064  

I guess you create one object of class `boost::compute::vector`, which in the end is just a wrapper class for an OpenCL buffer, and then you copy data from host (from some `std::vector`) to that device vector. Is that correct?

---

## Comment 4

> Username: ppacory  
> Created at: 2016-12-30 17:50:09 UTC  
> Url: https://github.com/boostorg/compute/issues/679#issuecomment-269800895  

how to allocate more than one buffer ?  
boost::compute copy ??

---

## Comment 5

> Username: ppacory  
> Created at: 2016-12-30 17:50:17 UTC  
> Url: https://github.com/boostorg/compute/issues/679#issuecomment-269800907  

#include <vector>  
#include <algorithm>  
#include <boost/compute.hpp>  
  
namespace compute = boost::compute;  
  
int main()  
{  
    // get the default compute device  
    compute::device gpu = compute::system::default_device();  
  
    // create a compute context and command queue  
    compute::context ctx(gpu);  
    compute::command_queue queue(ctx, gpu);  
  
    // generate random numbers on the host  
    std::vector<uint32_t> host_vector1(1000000000);  
    std::vector<uint32_t> host_vector2(1000000000);  
  
    // create vector on the device  
    compute::vector<uint32_t> device_vector1(host_vector1.size(), ctx);  
    compute::vector<uint32_t> device_vector2(host_vector2.size(), ctx);  
  
    // copy data to the device  
    compute::copy( host_vector1.begin(), host_vector1.end(), device_vector1.begin(), queue );  
    compute::copy( host_vector2.begin(), host_vector2.end(), device_vector2.begin(), queue );  
  
    // problem of memory at this point ...  
  
    return 0;  
}

---

## Comment 6

> Username: Ulfgard  
> Created at: 2016-12-30 18:32:14 UTC  
> Url: https://github.com/boostorg/compute/issues/679#issuecomment-269805777  

Hi,  
  
your single buffers are still exceeding the allowed maximum size by the device. Remove a zero and it will work.  
________________________________  
From: ppacory [notifications@github.com]  
Sent: Friday, December 30, 2016 6:50 PM  
To: boostorg/compute  
Cc: Subscribed  
Subject: Re: [boostorg/compute] Max buffer memory - CL_DEVICE_MAX_MEM_ALLOC_SIZE (#679)  
  
  
#include  
#include  
#include <boost/compute.hpp>  
  
namespace compute = boost::compute;  
  
int main()  
{  
// get the default compute device  
compute::device gpu = compute::system::default_device();  
  
// create a compute context and command queue  
compute::context ctx(gpu);  
compute::command_queue queue(ctx, gpu);  
  
// generate random numbers on the host  
std::vector<uint32_t> host_vector1(1000000000);  
std::vector<uint32_t> host_vector2(1000000000);  
  
// create vector on the device  
compute::vector<uint32_t> device_vector1(host_vector1.size(), ctx);  
compute::vector<uint32_t> device_vector2(host_vector2.size(), ctx);  
  
// copy data to the device  
compute::copy( host_vector1.begin(), host_vector1.end(), device_vector1.begin(), queue );  
compute::copy( host_vector2.begin(), host_vector2.end(), device_vector2.begin(), queue );  
  
// problem of memory at this point ...  
  
return 0;  
  
  
}  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/compute/issues/679#issuecomment-269800907>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AOWTBjitiE10MSDfIl13dVEwaOjG9c_Bks5rNURbgaJpZM4LYPYb>.

---

## Comment 7

> Username: jszuppe  
> Created at: 2016-12-30 18:32:19 UTC  
> Updated at: 2016-12-30 18:32:56 UTC  
> Url: https://github.com/boostorg/compute/issues/679#issuecomment-269805792  

Vector of 1000000000 `int32_t`s takes ~3814 MB, which is obviously more than 3071 MB. You just need to allocate less memory. Replace   
  
```cpp  
// generate random numbers on the host  
std::vector<uint32_t> host_vector1(1000000000);  
std::vector<uint32_t> host_vector2(1000000000);  
```  
  
with  
  
```cpp  
// generate random numbers on the host  
std::vector<uint32_t> host_vector1(700000000);  
std::vector<uint32_t> host_vector2(700000000);  
```  
  
It should help. The problem occurs when copying because allocating memory for buffer is a lazy operation, so it's performed when buffer is used for the first time (in your case - during copying). You can close this issue, since it's not a Boost.Compute-related.

---

## Comment 8

> Username: ppacory  
> Created at: 2016-12-30 18:48:33 UTC  
> Url: https://github.com/boostorg/compute/issues/679#issuecomment-269807600  

ok thks for your help

---

## Comment 9

> Username: ppacory  
> Created at: 2017-01-02 13:46:45 UTC  
> Url: https://github.com/boostorg/compute/issues/679#issuecomment-269974492  

just another question :    
with those memory size on a nvidia titanX  
Global Memory Size: 12287 MB  
Max Alloc Size: 3071 MB  
I never arrived to address more than 4Go, with different buffer or context  
Is there a way to override that ?
