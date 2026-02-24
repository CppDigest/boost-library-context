# #8 - test_copy_if build failure [Closed]

> Username: d-meiser  
> Created at: 2013-04-17 20:40:14 UTC  
> Updated at: 2013-04-19 00:07:22 UTC  
> Closed at: 2013-04-17 23:18:56 UTC  
> Url: https://github.com/boostorg/compute/issues/8  

I get the following error message:  
  
[ 32%] Building CXX object test/CMakeFiles/test_copy_if.dir/test_copy_if.cpp.o  
cd /home/scratch/boost.compute/build/test && /usr/bin/c++   -DBOOST_COMPUTE_DEBUG_KERNEL_COMPILATION -DBOOST_TEST_DYN_LINK -g -I/usr/local/cuda/include -I/home/dmeiser/software/boost-1_53_0-ser/include -I/home/scratch/boost.compute/include -I/home/scratch/boost.compute/test/../include    -o CMakeFiles/test_copy_if.dir/test_copy_if.cpp.o -c /home/scratch/boost.compute/test/test_copy_if.cpp  
/home/scratch/boost.compute/include/boost/compute/functional/geometry.hpp: In member function ‘void clip_points_below_plane::test_method()’:  
/home/scratch/boost.compute/include/boost/compute/functional/geometry.hpp:21: error: ‘template<class T> class boost::compute::dot’ is not a function,  
/home/scratch/boost.compute/include/boost/compute/lambda/functional.hpp:222: error:   conflict with ‘template<class Arg1, class Arg2> const typename boost::proto::result_of::make_expr<boost::proto::tagns_::tag::function, boost::compute::lambda::detail::dot_func, const Arg1&, const Arg2&, void, void, void, void, void, void, void, void, void>::type boost::compute::lambda::dot(const Arg1&, const Arg2&)’  
/home/scratch/boost.compute/test/test_copy_if.cpp:106: error:   in call to ‘dot’  
  
This could be a gcc issue (I'm trying to build with gcc 4.4.6). Will try to build with newer gcc and clang

---

## Comment 1

> Username: d-meiser  
> Created at: 2013-04-17 22:46:37 UTC  
> Url: https://github.com/boostorg/compute/issues/8#issuecomment-16540485  

An update on this issue: gcc 4.6.2, gcc 4.7.2, gcc 4.9.0, and clang 3.3  
work just fine.

---

## Comment 2

> Username: kylelutz  
> Created at: 2013-04-17 23:18:56 UTC  
> Url: https://github.com/boostorg/compute/issues/8#issuecomment-16541644  

Sounds good. GCC 4.4 is getting quite old now. I'll add some information about the minimum supported compiler versions (with 4.6 for GCC) to the README.  
  
Thanks!

---

## Comment 3

> Username: d-meiser  
> Created at: 2013-04-18 16:04:03 UTC  
> Url: https://github.com/boostorg/compute/issues/8#issuecomment-16585808  

Note however that gcc 4.4 is the system gcc on rhel 6 and similar. I'll try and see if there is an easy fix for this that allows to build with gcc 4.4.  
Cheers,  
Dominic

---

## Comment 4

> Username: kylelutz  
> Created at: 2013-04-19 00:07:22 UTC  
> Url: https://github.com/boostorg/compute/issues/8#issuecomment-16620700  

Sounds good. I don't have much time to devote towards older compilers, but if you find I fix I'll merge it in.  
  
Cheers,  
Kyle
