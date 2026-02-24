# #45 - Incompatibility with Thrust v1.10.0 (CUDA 11.2) [Closed]

> Username: lhofmann  
> Created at: 2021-01-13 08:47:28 UTC  
> Updated at: 2024-01-02 21:14:51 UTC  
> Closed at: 2024-01-02 21:14:51 UTC  
> Url: https://github.com/boostorg/odeint/issues/45  

Using Thrust v1.10.0 (included in CUDA v11.2), compiling the thrust example (`test_external/thrust`) fails with  
```  
../../include/boost/numeric/odeint/external/thrust/thrust_algebra_dispatcher.hpp(97): error: class template "boost::numeric::odeint::algebra_dispatcher" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_operations_dispatcher.hpp(96): error: class template "boost::numeric::odeint::operations_dispatcher" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(176): error: class template "boost::numeric::odeint::is_resizeable" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(177): error: class template "boost::numeric::odeint::resize_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(177): error: class template "boost::numeric::odeint::resize_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(178): error: class template "boost::numeric::odeint::same_size_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(178): error: class template "boost::numeric::odeint::same_size_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(179): error: class template "boost::numeric::odeint::copy_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(179): error: class template "boost::numeric::odeint::copy_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(179): error: class template "boost::numeric::odeint::copy_impl" has already been defined  
```  
  
The problem arises, because in Thrust v1.10.0 the types `thrust::cpp::vector`, `thrust::omp::vector`, `thrust::tbb::vector` and `thrust::cuda::vector` are all aliases of the same type `thrust::detail::vector_base`.

---

## Comment 1

> Username: droyktton  
> Created at: 2022-04-29 23:18:09 UTC  
> Url: https://github.com/boostorg/odeint/issues/45#issuecomment-1113856529  

Similar problem with Thrust 1.15.0, running all thrust examples. The fix #46 does not solve it for me.  
  
```  
thrust$ nvcc --version  
nvcc: NVIDIA (R) Cuda compiler driver  
Copyright (c) 2005-2022 NVIDIA Corporation  
Built on Thu_Feb_10_18:23:41_PST_2022  
Cuda compilation tools, release 11.6, V11.6.112  
Build cuda_11.6.r11.6/compiler.30978841_0  
  
```  
  
```  
thrust$ nvcc -O3 -I../../include/ phase_oscillator_chain.cu  
/usr/include/boost/fusion/container/vector/vector.hpp(195): warning #20012-D: __host__ annotation is ignored on a function("vector_data") that is explicitly defaulted on its first declaration  
  
/usr/include/boost/fusion/container/vector/vector.hpp(195): warning #20012-D: __device__ annotation is ignored on a function("vector_data") that is explicitly defaulted on its first declaration  
  
/usr/include/boost/fusion/container/vector/vector.hpp(273): warning #20012-D: __host__ annotation is ignored on a function("vector") that is explicitly defaulted on its first declaration  
  
/usr/include/boost/fusion/container/vector/vector.hpp(273): warning #20012-D: __device__ annotation is ignored on a function("vector") that is explicitly defaulted on its first declaration  
  
/usr/include/boost/fusion/support/unused.hpp(25): warning #20012-D: __host__ annotation is ignored on a function("unused_type") that is explicitly defaulted on its first declaration  
  
/usr/include/boost/fusion/support/unused.hpp(25): warning #20012-D: __device__ annotation is ignored on a function("unused_type") that is explicitly defaulted on its first declaration  
  
/usr/include/boost/fusion/support/unused.hpp(31): warning #20012-D: __host__ annotation is ignored on a function("unused_type") that is explicitly defaulted on its first declaration  
  
/usr/include/boost/fusion/support/unused.hpp(31): warning #20012-D: __device__ annotation is ignored on a function("unused_type") that is explicitly defaulted on its first declaration  
  
../../include/boost/numeric/odeint/external/thrust/thrust_algebra_dispatcher.hpp(97): error: class template "boost::numeric::odeint::algebra_dispatcher" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_operations_dispatcher.hpp(96): error: class template "boost::numeric::odeint::operations_dispatcher" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(176): error: class template "boost::numeric::odeint::is_resizeable" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(177): error: class template "boost::numeric::odeint::resize_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(177): error: class template "boost::numeric::odeint::resize_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(178): error: class template "boost::numeric::odeint::same_size_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(178): error: class template "boost::numeric::odeint::same_size_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(179): error: class template "boost::numeric::odeint::copy_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(179): error: class template "boost::numeric::odeint::copy_impl" has already been defined  
  
../../include/boost/numeric/odeint/external/thrust/thrust_resize.hpp(179): error: class template "boost::numeric::odeint::copy_impl" has already been defined  
  
10 errors detected in the compilation of "phase_oscillator_chain.cu".  
  
```

---

## Comment 2

> Username: SiddhantKadwe  
> Created at: 2023-02-01 15:19:20 UTC  
> Url: https://github.com/boostorg/odeint/issues/45#issuecomment-1412236325  

Are there any solutions to the above errors?  
I'm also receiving the exact same errors.

---

## Comment 3

> Username: lhofmann  
> Created at: 2023-02-01 18:37:51 UTC  
> Url: https://github.com/boostorg/odeint/issues/45#issuecomment-1412538610  

I have been using the following hacky workaround:  
  
1.  apply my patch https://github.com/boostorg/odeint/pull/46  
2.  copy the directory `include/boost/numeric/odeint/external/thrust` to some location `odeint_thrust/`  
3.  replace all references to `boost/numeric/odeint/external/thrust` in  `odeint_thrust/` by  `odeint_thrust/`  
4.  include in the following order:  
```cpp  
#include <boost/numeric/odeint.hpp>  
#include <odeint_thrust/thrust.hpp>  
#include <thrust/device_vector.h>  
#include <thrust/iterator/counting_iterator.h>  
```
