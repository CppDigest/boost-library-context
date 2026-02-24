# #743 - Event callbacks hanging in OSX [Closed]

> Username: bruno-j-nicoletti  
> Created at: 2017-09-12 16:45:32 UTC  
> Updated at: 2017-09-13 21:57:53 UTC  
> Closed at: 2017-09-13 21:57:53 UTC  
> Url: https://github.com/boostorg/compute/issues/743  

Hi,  
  
I have a problem with events in boost::compute on OSX. The code below replicates the issue. I have a chain of enqueued actions, starting with a user event. The final action in the chain has an associated callback which never gets called unless I explicitly wait on the associated event. In the code below the 'future.get()' never returns. If I code the equivalent in raw openCL I don't get the problem, so it looks like a bug in boost::compute. Took me most of a day to isolate it to compute::boost.  
  
My configuration is...  
  
- OSX 10.12.6  
- boost::compute 1.64.0  
- Apple LLVM version 8.1.0 (clang-802.0.42)  
  
This is the borked boost::compute program...  
  
```C++  
#include <iostream>  
#include <future>  
#include <thread>  
#include <vector>  
  
#include "boost/compute.hpp"  
  
static const size_t kNElements = 1024 * 1024;  
static const size_t kElementSize = sizeof(int);  
static const size_t kBufferSize = kNElements * kElementSize;  
  
int main()  
{  
  std::vector<int> hostDataSrc(kNElements);  
  std::vector<int> hostDataDst(kNElements);  
  auto platforms = boost::compute::system::platforms();  
  
  for(auto &platform : platforms) {  
    for(auto &device : platform.devices()) {  
      std::cout << "Device ='" <<  device.name() << "'\n";  
      boost::compute::context context(device);  
  
      // make a queue  
      boost::compute::command_queue queue(context, device);  
  
      // make a buffer  
      boost::compute::buffer buffer(context, kBufferSize);  
  
      // make a user event  
      boost::compute::user_event userEvent(context);  
  
      // copy into the context, waiting on the user event  
      auto copyOnEvent = queue.enqueue_write_buffer_async(buffer, 0, kBufferSize, &hostDataSrc.front(), {userEvent});  
  
      // copy out of the context, waiting on the write  
      auto copyOutEvent = queue.enqueue_read_buffer_async(buffer, 0, kBufferSize, &hostDataDst.front(), {copyOnEvent});  
  
      // make a promise/future pair  
      std::promise<bool> promise;  
      auto future = promise.get_future();  
  
      // set up a callback on the copyOutEvent to set the promise  
      copyOutEvent.set_callback([&]() {promise.set_value(true);});  
  
      // kick it all off  
      userEvent.set_status(CL_COMPLETE);  
  
      // now wait for the future to be done.  
      //copyOutEvent.wait(); //<--- uncomment this and it works  
      future.get(); //<--- hangs here indicating callback has not been called  
    }  
  }  
  return 0;  
}  
```  
  
This is the working raw OpenCL equivalent ....  
  
```C++  
#include <array>  
#include <future>  
#include <iostream>  
#include <string>  
#include <vector>  
  
#include "OpenCL/cl.h"  
  
#define CheckErr(X)                                                     \  
{                                                                       \  
  auto ret__ = (X);                                                       \  
  if(ret__ != CL_SUCCESS) {                                               \  
    std::cerr << "CL failure " << ret__ << " line " << __LINE__ << std::endl; \  
    exit(1);                                                            \  
  }                                                                     \  
}  
  
#define ContinueErr(X)                                                     \  
{                                                                       \  
  auto ret__ = (X);                                                       \  
  if(ret__ != CL_SUCCESS) {                                               \  
    std::cerr << "CL failure " << ret__ << " line " << __LINE__ << std::endl; \  
    continue;                                                            \  
  }                                                                     \  
}  
  
void CL_CALLBACK ContextNotify(const char *errinfo,  
                               const void  *private_info,  
                               size_t  cb,  
                               void  *user_data)  
{  
  std::cout << "Context error " << errinfo << std::endl;  
}  
  
  
// callback to set promise  
std::unique_ptr<std::promise<bool>> gPromise;  
std::unique_ptr<std::future<bool>> gFuture;  
  
void CL_CALLBACK EventCallback(cl_event event, cl_int, void* pUserData)  
{  
  gPromise->set_value(true);  
}  
  
  
////////////////////////////////////////////////////////////////////////////////  
// main  
int main(int argc, char **argv)  
{  
  std::array<cl_device_type, 2> deviceTypes = {CL_DEVICE_TYPE_CPU, CL_DEVICE_TYPE_GPU};  
  
  static const size_t kNElements = 1024 * 1024;  
  static const size_t kElementSize = sizeof(int);  
  static const size_t kBufferSize = kNElements * kElementSize;  
  
  // make some space to transfer data into and out of  
  std::vector<int> hostDataSrc(kNElements);  
  std::vector<int> hostDataDst(kNElements);  
  
  cl_uint nPlatforms;  
  cl_platform_id platformIDs[1024];  
  
  CheckErr(clGetPlatformIDs(1024,  
                            platformIDs,  
                            &nPlatforms));  
  for(int i = 0; i < nPlatforms; ++i) {  
    auto platformID = platformIDs[i];  
    for(int j = 0; j < deviceTypes.size(); ++j) {  
      cl_device_id deviceIDs[1024];  
      cl_uint nDevices;  
      ContinueErr(clGetDeviceIDs(platformID, deviceTypes[j], 1024, deviceIDs, &nDevices));  
      for(int d = 0; d < nDevices; ++d) {  
        // make up a promise/future pair to wait on  
        gPromise = std::make_unique<std::promise<bool>>();  
        gFuture = std::make_unique<std::future<bool>>();  
        *gFuture = gPromise->get_future();  
  
        // get a device  
        auto deviceID = deviceIDs[d];  
        char str[1024];  
        CheckErr(clGetDeviceInfo(deviceID, CL_DEVICE_NAME, 1024, str, nullptr));  
        std::cout << "Device = '" << str << "'\n";  
  
        //make a context  
        cl_context_properties props[] = {CL_CONTEXT_PLATFORM, (cl_context_properties) platformID, 0};  
        cl_int ret;  
        auto context = clCreateContext(props,  
                                       1,  
                                       &deviceID,  
                                       &ContextNotify,  
                                       nullptr,  
                                       &ret);  
        if(ret != CL_SUCCESS) {  
          std::cout << "Failed to make context, err is " << ret << std::endl;  
          continue;  
        }  
  
        // make a queue  
        cl_command_queue queue = clCreateCommandQueue(context,  
                                                      deviceID,  
                                                      0,  
                                                      &ret);  
        if(ret != CL_SUCCESS) {  
          std::cout << "Failed to make queue, err is " << ret << std::endl;  
          continue;  
        }  
  
        // make a buffer in the context  
        auto buffer = clCreateBuffer(context, CL_MEM_READ_WRITE, kBufferSize, NULL, NULL);  
  
        // make a user event  
        cl_int err;  
        cl_event userEvent = clCreateUserEvent(context, &err);  
  
        // enqueue a write, waits on user event  
        cl_event writeEvent;  
        clEnqueueWriteBuffer(queue, buffer, CL_FALSE, 0, kBufferSize, &hostDataSrc.front(), 1, &userEvent, &writeEvent);  
  
        // enqueue a read, waits on the write  
        cl_event readEvent;  
        clEnqueueReadBuffer(queue, buffer, CL_FALSE, 0, kBufferSize, &hostDataDst.front(), 1, &writeEvent, &readEvent);  
  
        // add a callback to the read which sets the promise  
        ret = clSetEventCallback(readEvent, CL_COMPLETE, &EventCallback, NULL);  
  
        // start it all off  
        clSetUserEventStatus(userEvent, CL_COMPLETE);  
  
        // wait for it to be done  
        gFuture.get();  
      }  
    }  
  }  
  return 0;  
}  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-09-12 20:51:35 UTC  
> Updated at: 2017-09-12 21:04:23 UTC  
> Url: https://github.com/boostorg/compute/issues/743#issuecomment-328980777  

Your Boost.Compute code works fine on NVIDIA and Intel (CPU) OpenCL environments on Linux. I don't see any OS-specific parts of code in `event.hpp`.   
  
Do all Boost.Compute tests pass on your environment? If they do, look at `event_to_std_future` test in `test_event.cpp`.  
  
You can try adding arguments `cl_event event, cl_int status, void *user_data` to your lambda, and passing promise in `user_data` instead of capturing it, so the lambda expression matches `set_callback`  [`event.hpp:L229`](https://github.com/boostorg/compute/blob/a965a8dab6fe0b997bf3a17c298653a34d7c1714/include/boost/compute/event.hpp#L229) instead of [`event.hpp:L249`](https://github.com/boostorg/compute/blob/a965a8dab6fe0b997bf3a17c298653a34d7c1714/include/boost/compute/event.hpp#L249):  
  
```cpp  
copyOutEvent.set_callback(  
            [](cl_event event, cl_int status, void *user_data) -> void  
            {  
                auto promise = static_cast<std::promise<bool> *>(user_data);  
                promise->set_value(true);  
            },  
            CL_COMPLETE,  
            &promise  
        );  
```

---

## Comment 2

> Username: bruno-j-nicoletti  
> Created at: 2017-09-12 21:40:31 UTC  
> Url: https://github.com/boostorg/compute/issues/743#issuecomment-328992358  

Thanks for the reply. I've built and run the compute tests, and the event test is fine, though there was a compile error in **test_extrema.cpp**.  
  
I changed the callback as you suggested, and it still fails.  
  
However, the ```event_to_std_future``` test has a ```queue.flush()``` command before the wait on the future. If I insert a flush it all works fine. Goodness knows what is really going on, but this is  at least a work around.

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-09-12 21:55:35 UTC  
> Url: https://github.com/boostorg/compute/issues/743#issuecomment-328995734  

>  though there was a compile error in test_extrema.cpp.  
  
Could you post it here?  
  
> I changed the callback as you suggested, and it still fails.  
  
:(  
  
>  However, the event_to_std_future test has a queue.flush() command before the wait on the future. If I insert a flush it all works fine. Goodness knows what is really going on, but this is at least a work around.  
  
That's ~good. I don't have OSX, so I can't check what's happening. However, that also means I can just blame OSX's OpenCL implementation ^^ ;)

---

## Comment 4

> Username: bruno-j-nicoletti  
> Created at: 2017-09-12 22:49:57 UTC  
> Url: https://github.com/boostorg/compute/issues/743#issuecomment-329006090  

Clang produces....  
  
> darwin.compile.c++ ../../../bin.v2/libs/compute/test/test_extrema.test/darwin-4.2.1/debug/test_extrema.o  
> test_extrema.cpp:123:14: error: no member named 'cerr' in namespace 'std'  
>         std::cerr  
>         ~~~~~^  
> test_extrema.cpp:225:14: error: no member named 'cerr' in namespace 'std'  
>         std::cerr  
>         ~~~~~^  
> 2 errors generated.  
>   
>     "g++"  -ftemplate-depth-128 -O0 -fno-inline -Wall -g -dynamic -gdwarf-2 -fexceptions -fPIC -arch x86_64  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_TEST_DYN_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_DYN_LINK=1  -I"../../.." -c -o "../../../bin.v2/libs/compute/test/test_extrema.test/darwin-4.2.1/debug/test_extrema.o" "test_extrema.cpp"  
>   
>

---

## Comment 5

> Username: bruno-j-nicoletti  
> Created at: 2017-09-12 22:51:14 UTC  
> Updated at: 2017-09-12 22:51:27 UTC  
> Url: https://github.com/boostorg/compute/issues/743#issuecomment-329006309  

Also, I'm not sure if it is a driver issue. As the equivalent raw OpenCL code works fine.

---

## Comment 6

> Username: jszuppe  
> Created at: 2017-09-13 17:51:03 UTC  
> Updated at: 2017-09-13 18:03:24 UTC  
> Url: https://github.com/boostorg/compute/issues/743#issuecomment-329245856  

> Also, I'm not sure if it is a driver issue. As the equivalent raw OpenCL code works fine.  
  
Yeah, I was just joking, I can't be sure if it's a driver issue. However, I just don't see any bug in Boost.Compute code that would be specific only to OSX. Also, to be honest, I think you have to perform `queue.flush()` operation, because a non-blocking command may reside in the queue until it is flushed either explicitly (a call to `clFlush`) or implicitly by some other command. I am not sure why the version without flush works.   
  
OpenCL specification is quite complex and long, but IIRC, unless you call `clFlush`, `clWaitForEvents`, or enqueue a blocking command, it's possible (it's in line with specification) that your code will hung on `future.get()`.

---

## Comment 7

> Username: Ulfgard  
> Created at: 2017-09-13 17:57:54 UTC  
> Url: https://github.com/boostorg/compute/issues/743#issuecomment-329247789  

i would argue that it is a dangerous design flaw if a future blocks the current (possibly only) thread without ensuring that it will eventually unblock(i.e. flush the queue).

---

## Comment 8

> Username: bruno-j-nicoletti  
> Created at: 2017-09-13 21:57:53 UTC  
> Url: https://github.com/boostorg/compute/issues/743#issuecomment-329308785  

So it sounds like it is just lucky (timing issues?) that the raw OpenCL is working without the queue flush while the boost version isn't . Thanks for the comments and feedback folks, I really appreciate it.
