# #473 - Deprecated OpenCL function is used [Closed]

> Username: jszuppe  
> Created at: 2015-06-28 17:57:33 UTC  
> Updated at: 2016-05-06 10:14:22 UTC  
> Closed at: 2015-07-25 13:03:36 UTC  
> Url: https://github.com/boostorg/compute/issues/473  

In `boost::compute::command_queue::enqueue_marker()` depreceted  OpenCL function (since 1.2) - `clEnqueueMarker(cl_command_queue, _cl_event**)`  - is used.  
  
``` cpp  
#ifdef CL_VERSION_1_2  
if(get_device().check_version(1, 2)){  
    ret = clEnqueueMarkerWithWaitList(m_queue, 0, 0, &event_.get());  
} else  
#endif  
{  
    ret = clEnqueueMarker(m_queue, &event_.get());  // HERE  
}  
```  
  
I get that despite `CL_VERSION_1_2` being defined we want to ensure that everything works fine even if used device does not support OpenCL 1.2, but, well, it leads to using deprecated function.   
  
Also few line above:  
  
``` cpp  
 /// Enqueues a barrier in the queue.  
void enqueue_barrier()  
{  
    BOOST_ASSERT(m_queue != 0);  
  
    #ifdef CL_VERSION_1_2  
    clEnqueueBarrierWithWaitList(m_queue, 0, 0, 0);  
    #else  
    clEnqueueBarrier(m_queue);  
    #endif  
}  
```  
  
it's not checked if device supports OpenCL 1.2 when `CL_VERSION_1_2` is defined.   
  
Simply adding `#else` directive doesn't work. I think it should be decided how to proceed in such situations. Till now I thought that it's assumed that when you use `cl.h` with OpenCL 1.2 defined all devices (you want to use) should support it.  
  
Environment:  
- AMD APP 2.9.1  
- AMD Radeon HD 7700  
- gcc 4.8.2  
- -Wall -pedantic -Werror -Wno-variadic-macros -Wno-long-long -Wno-shadow  
  
Temporary hotfix:  
https://github.com/haahh/compute/tree/hotfix_enqueue_marker_280615

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-07-02 02:27:06 UTC  
> Url: https://github.com/boostorg/compute/issues/473#issuecomment-117878550  

Yeah, I think we should follow a similar pattern to other functions which check (at runtime) if the device supports OpenCL v1.2 else fall back to the v1.1 method.

---

## Comment 2

> Username: jszuppe  
> Created at: 2015-07-02 05:39:26 UTC  
> Updated at: 2015-07-02 17:07:11 UTC  
> Url: https://github.com/boostorg/compute/issues/473#issuecomment-117914340  

Ok,  so I'll just make my gcc suppress deprecated function used warning. I think that should also be done in travis run configuration (or -Werror should be removed), otherwise it won't compile when `CL_VERSION_1_2` is defined.

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-07-25 13:03:36 UTC  
> Url: https://github.com/boostorg/compute/issues/473#issuecomment-124845407  

OK, I doubt Travis CL will get OpenCL 1.2 version (or newer) and as long as you don't compile with `-Werror` using deprecated OpenCL function causes just warnings (inserted below), so I'm closing the issue.  
  
``` bash  
/[path]/compute/include/boost/compute/command_queue.hpp: In member function ‘boost::compute::event boost::compute::command_queue::enqueue_marker()’:  
/[path]/compute/include/boost/compute/command_queue.hpp:1219:19: warning: ‘cl_int clEnqueueMarker(cl_command_queue, _cl_event**)’ is deprecated (declared at /opt/AMDAPPSDK-2.9-1/include/CL/cl.h:1194) [-Wdeprecated-declarations]  
             ret = clEnqueueMarker(m_queue, &event_.get());  
                   ^  
/[path]/compute/include/boost/compute/command_queue.hpp:1219:57: warning: ‘cl_int clEnqueueMarker(cl_command_queue, _cl_event**)’ is deprecated (declared at /opt/AMDAPPSDK-2.9-1/include/CL/cl.h:1194) [-Wdeprecated-declarations]  
             ret = clEnqueueMarker(m_queue, &event_.get());  
  
```

---

## Comment 4

> Username: robertwgh  
> Created at: 2016-05-06 07:02:46 UTC  
> Url: https://github.com/boostorg/compute/issues/473#issuecomment-217364095  

I met this issues too.  
  
Why not just use standard OpenCL Macros like CL_USE_DEPRECATED_OPENCL_1_1_APIS to deal with deprecated APIs.   
  
Actually, the code above can be written using standard CL Macro like this:  
  
```  
  
 /// Enqueues a barrier in the queue.  
void enqueue_barrier()  
{  
    BOOST_ASSERT(m_queue != 0);  
    #if defined(CL_USE_DEPRECATED_OPENCL_1_1_APIS) || (defined(CL_VERSION_1_1) && !defined(CL_VERSION_1_2))   
    clEnqueueBarrier(m_queue);  
    #else  
     clEnqueueBarrierWithWaitList(m_queue, 0, 0, 0);  
    #endif  
}  
```

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-05-06 07:14:26 UTC  
> Url: https://github.com/boostorg/compute/issues/473#issuecomment-217368155  

We want to user to be able to link Boost.Compute against OpenCL 2.0 and use  
2.0 cl.h and still be able to use his OpenCL 1.1 device. That's why we need  
to decide in the runtime which function we'll use.  
  
However, I think we've resolved that issue with deprecated-function-is-used  
warnings in Boost.Compute by using mvsc, gcc and clang pragmas that disable  
this kind of warning in certain places.  
6 maj 2016 09:02 "robertwgh" notifications@github.com napisał(a):  
  
> I met this issues too.  
>   
> Why not just use standard OpenCL Macros like  
> CL_USE_DEPRECATED_OPENCL_1_1_APIS to deal with deprecated APIs.  
>   
> Actually, the code above can be written using standard CL Macro like this:  
>   
>  /// Enqueues a barrier in the queue.  
> void enqueue_barrier()  
> {  
>     BOOST_ASSERT(m_queue != 0);  
>     #if defined(CL_USE_DEPRECATED_OPENCL_1_1_APIS) || (defined(CL_VERSION_1_1) && !defined(CL_VERSION_1_2))  
>     clEnqueueBarrier(m_queue);  
>     #else  
>      clEnqueueBarrierWithWaitList(m_queue, 0, 0, 0);  
>     #endif  
> }  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/issues/473#issuecomment-217364095

---

## Comment 6

> Username: robertwgh  
> Created at: 2016-05-06 09:25:17 UTC  
> Url: https://github.com/boostorg/compute/issues/473#issuecomment-217395563  

OK. Thanks.  
I now understand how this works. When building with Boost::Compute, we enable all macros such as CL_USE_DEPRECATED_OPENCL_1_1_APIS, CL_USE_DEPRECATED_OPENCL_1_2_APIS and so on. And at run time, we choose the right API based on the dynamic library used.

---

## Comment 7

> Username: jszuppe  
> Created at: 2016-05-06 10:14:22 UTC  
> Url: https://github.com/boostorg/compute/issues/473#issuecomment-217404112  

You can check out changes I've introduced in https://github.com/boostorg/compute/pull/566, so user does not need to declare those 'CL_USE_DEPRECATED_' macros.
