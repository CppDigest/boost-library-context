# #445 - Errors on AIX with C++20 [Open]

> Username: techmaddy  
> Created at: 2025-03-02 13:20:53 UTC  
> Updated at: 2025-08-14 12:11:25 UTC  
> Url: https://github.com/boostorg/asio/issues/445  

I am trying to compile the boost beast sample programs using  boost-1.87.0  
  
I am getting the below errors:  
  
```  
In file included from /boost/boost/asio.hpp:38:  
In file included from /boost/boost/asio/basic_readable_pipe.hpp:39:  
In file included from /boost/boost/asio/detail/reactive_descriptor_service.hpp:32:  
In file included from /boost/boost/asio/detail/descriptor_ops.hpp:174:  
/boost/boost/asio/detail/impl/descriptor_ops.ipp:197:25: error: implicit conversion from 'unsigned long' to 'int' changes value from 18446744071562356350 to -2147195266 [-Werror,-Wconstant-conversion]  
  197 |     result = ::ioctl(d, FIONBIO, &arg);  
      |              ~~         ^~~~~~~  
```  
  
So, was wondering if any have faced the same issue?

---

## Comment 1

> Username: techmaddy  
> Created at: 2025-03-04 20:05:46 UTC  
> Url: https://github.com/boostorg/asio/issues/445#issuecomment-2698779068  

I ended up updating the files as below where ever FIONBIO is being used.   
```  
#if defined(_AIX)  
    result = ::ioctl(d, static_cast<int>(static_cast<unsigned int>(FIONBIO)), &arg);  
#else  
    result = ::ioctl(d, FIONBIO, &arg);  
#endif     
```

---

## Comment 2

> Username: mbs-c  
> Created at: 2025-08-14 12:11:25 UTC  
> Url: https://github.com/boostorg/asio/issues/445#issuecomment-3188232801  

It's worth noting that only 64-bit builds (`OBJECT_MODE=64`) are affected.
