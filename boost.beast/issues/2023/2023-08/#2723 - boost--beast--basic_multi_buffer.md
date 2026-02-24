# #2723 - boost::beast::basic_multi_buffer [Closed]

> Username: Sivasundareswaran  
> Created at: 2023-08-17 09:50:34 UTC  
> Updated at: 2024-01-20 21:45:47 UTC  
> Closed at: 2024-01-18 12:12:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2723  

Hi Team ,  
we are facing thread lock issue some times while consuming multi_buffer data for cleanup ,please find the below back trace gcore.  
  
kindly help us to overcome this issue .  
  
#0  0x00007f544128565c in __lll_lock_wait_private () from /lib64/libc.so.6  
#1  0x00007f544120147c in _L_lock_5314 () from /lib64/libc.so.6  
#2  0x00007f54411fa078 in _int_free () from /lib64/libc.so.6  
#3  0x000000000054683a in __gnu_cxx::new_allocator<boost::tt_align_ns::a8>::deallocate (this=0x7f539a7fb777,  
    __p=0x7f5380002a60) at /opt/rh/devtoolset-7/root/usr/include/c++/7/ext/new_allocator.h:125  
#4  0x0000000000540026 in std::allocator_traits<std::allocator<boost::tt_align_ns::a8> >::deallocate (__a=...,  
    __p=0x7f5380002a60, __n=67) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/alloc_traits.h:462  
#5  0x0000000000538e70 in boost::beast::basic_multi_buffer<std::allocator<char> >::destroy (this=0x7f5420029568, e=...)  
    at /opt/boost/include/boost/beast/core/impl/multi_buffer.hpp:1223  
#6  0x00000000005330b5 in boost::beast::basic_multi_buffer<std::allocator<char> >::consume (this=0x7f5420029568, n=49)  
    at /opt/boost/include/boost/beast/core/impl/multi_buffer.hpp:1018

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-17 11:00:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2723#issuecomment-1682076024  

What is the issue?

---

## Comment 2

> Username: Sivasundareswaran  
> Created at: 2023-08-17 11:15:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2723#issuecomment-1682104232  

some times basic_multi_buffer consume getting thread lock due to that application is hanging

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-17 12:59:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2723#issuecomment-1682244435  

Sure, but what blocks is a call into thr libc deallocation. Are you doing some custom allocations in your application?

---

## Comment 4

> Username: ashtum  
> Created at: 2023-12-31 08:36:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2723#issuecomment-1872858123  

@Sivasundareswaran Is this issue still open?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2024-01-20 21:45:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2723#issuecomment-1902272366  

`multi_buffer` is not designed to be thread safe.
