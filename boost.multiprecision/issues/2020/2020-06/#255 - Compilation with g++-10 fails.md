# #255 - Compilation with g++-10 fails: [Closed]

> Username: NAThompson  
> Created at: 2020-06-28 16:09:47 UTC  
> Updated at: 2020-06-28 18:12:32 UTC  
> Closed at: 2020-06-28 16:11:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/255  

```  
                 from test/tanh_sinh_quadrature_test.cpp:15:  
../../boost/multiprecision/cpp_int/intel_intrinsics.hpp: In function ‘unsigned char boost::multiprecision::detail::subborrow_limb(unsigned char, boost::multiprecision::limb_type, boost::multiprecision::limb_type, boost::multiprecision::limb_type*)’:  
../../boost/multiprecision/cpp_int/intel_intrinsics.hpp:82:11: error: ‘__builtin_ia32_subborrow_u64’ was not declared in this scope; did you mean ‘__builtin_ia32_sbb_u64’?  
   82 |    return __builtin_ia32_subborrow_u64(carry, a, b, reinterpret_cast<cast_type*>(p_result));  
      |           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
      |           __builtin_ia32_sbb_u64  
compilation terminated due to -Wfatal-errors.  
Makefile:24: recipe for target 'test/tanh_sinh_quadrature_test.x' failed  
$ g++-10 --version  
g++-10 (Ubuntu 10.1.0-2ubuntu1~18.04) 10.1.0  
Copyright (C) 2020 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-06-28 16:11:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/255#issuecomment-650787236  

Sorry for the noise, looks like this is fixed on develop.

---

## Comment 2

> Username: madhur4127  
> Created at: 2020-06-28 18:12:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/255#issuecomment-650802214  

Yes, this is fixed in `develop`. [Reference](https://github.com/boostorg/multiprecision/pull/234#issuecomment-636875903)
