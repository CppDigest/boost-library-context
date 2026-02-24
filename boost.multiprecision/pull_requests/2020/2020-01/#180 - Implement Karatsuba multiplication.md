# #180 Implement Karatsuba multiplication. [Merged]

> Username: jzmaddock  
> Created at: 2020-01-05 18:51:12 UTC  
> Updated at: 2021-01-30 16:09:56 UTC  
> Merged at: 2020-01-07 19:24:37 UTC  
> Closed at: 2020-01-07 19:24:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/180  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-01-05 18:52:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/180#issuecomment-570937263  

@ckormanyos : you will hopefully find the code quite readable now.

---

## Comment 2

> Username: madhur4127  
> Created_at: 2020-01-06 08:27:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/180#issuecomment-571050250  

TOOLSET=gcc COMPILER=g++-6 CXXSTD=gnu++11 TEST_SUITE=concepts  
```  
../../../boost/multiprecision/cpp_int.hpp:1345:60: error: constexpr constructor does not have empty body  
        : base_type(data, offset, len) { this->normalize(); }  
```  
TOOLSET=clang COMPILER=clang++ CXXSTD=c++14 TEST_SUITE="conversions performance"  
```  
In file included from ../performance/voronoi_performance.cpp:12:  
../../../boost/polygon/detail/voronoi_ctypes.hpp:454:12: error: call to 'abs' is ambiguous  
    return (std::abs)(count_);  
```  
Environment: ARGS=--toolset=msvc-12.0 address-model=32, TARGETS=conversions misc compile_fail examples concepts  
```  
..\..\..\boost/multiprecision/cpp_int.hpp(232) : error C2797: 'boost::multiprecision::backends::cpp_int_base<0,-1,signed_magnitude,unchecked,Allocator,false>::data_type::ld': list initialization inside member initializer list or non-static data member initializer is not implemented  
        with  
        [  
            Allocator=std::allocator<boost::multiprecision::limb_type>  
        ]  
```

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2020-01-06 09:42:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/180#issuecomment-571072226  

> you will hopefully find the code quite readable now.  
  
Yes. It is very clear and beautiful. Thank you for your help and patience.  
  
I feel like we can really move forward to working on some of the constants and transcendentals now in regions of many thousands of digits. And I will now begin working on this.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-01-07 18:29:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/180#issuecomment-571711749  

>I don't understand why the build on Travis failed for: https://travis-ci.org/boostorg/multiprecision/jobs/633439737?utm_medium=notification&utm_source=github_status  
  
Random bad mojo by the looks of it, restarting...

---
