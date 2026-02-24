# #68 - error C2672: 'boost::mp11::detail::cx_count_if': no matching overloaded function found on NVCC [Closed]

> Username: ZbigniewRA  
> Created at: 2021-11-18 08:13:37 UTC  
> Updated at: 2021-11-19 02:04:44 UTC  
> Closed at: 2021-11-19 02:04:44 UTC  
> Url: https://github.com/boostorg/mp11/issues/68  

**Issue description:**  
This is the same problem as #62, but when compiling CUDA code.  
To be specific I use mp11 from boost 1.77 in a `.cu` file (but not even in the GPU code).  
I use VS 2019 (latest, so 16.11.7) and CUDA (latest, so 11.5).  
  
(Please note that on boost 1.76 everything compiles fine.)  
  
Compilation succeeds after changing line 93 in `mp_count.hpp` from this:  
```  
#if defined( BOOST_MP11_HAS_CXX14_CONSTEXPR ) && !BOOST_MP11_WORKAROUND( BOOST_MP11_MSVC, < 1930 )  
```  
to this:  
```  
#if defined( BOOST_MP11_HAS_CXX14_CONSTEXPR ) && 0  
```  
  
**ErrorMessage:**  
```  
boost/mp11/detail/mp_count.hpp(111): error C2672: 'boost::mp11::detail::cx_count_if': no matching overloaded function found  
boost/mp11/detail/mp_count.hpp(112): note: see reference to class template instantiation 'boost::mp11::detail::mp_count_if_impl<L<T...>,P>' being compiled  
boost/mp11/detail/mp_count.hpp(111): error C3207: 'boost::mp11::detail::cx_count_if': invalid template argument for 'P', class template expected  
boost/mp11/detail/mp_count.hpp(95): note: see declaration of 'boost::mp11::detail::cx_count_if'  
```  
  
**Version details:**  
CMake reports the following toolchain versions:  
```  
-- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19043.  
-- The CXX compiler identification is MSVC 19.29.30137.0  
-- The CUDA compiler identification is NVIDIA 11.5.50  
```  
Output of `nvcc --version`:  
```  
nvcc: NVIDIA (R) Cuda compiler driver  
Copyright (c) 2005-2021 NVIDIA Corporation  
Built on Mon_Sep_13_20:11:50_Pacific_Daylight_Time_2021  
Cuda compilation tools, release 11.5, V11.5.50  
Build cuda_11.5.r11.5/compiler.30411180_0  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-11-18 12:01:25 UTC  
> Updated at: 2021-11-18 15:10:59 UTC  
> Url: https://github.com/boostorg/mp11/issues/68#issuecomment-972801600  

Looks like `__CUDACC__` is being defined, so the MSVC workarounds don't activate. Fixing this should in principle be trivial, but since I have no test coverage for CUDA at the moment, I can't be sure.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-11-18 12:09:31 UTC  
> Url: https://github.com/boostorg/mp11/issues/68#issuecomment-972807480  

Something like this https://github.com/boostorg/mp11/commit/a4d7c46670dc7b354c695e8c2c84b2dc44cdada7 should fix it.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-11-19 02:04:44 UTC  
> Url: https://github.com/boostorg/mp11/issues/68#issuecomment-973659718  

Should be fixed now.
