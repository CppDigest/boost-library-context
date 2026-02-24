# #49 - Boost.Random uses deprecated boost/pending/integer_log2.hpp [Closed]

> Username: dscharrer  
> Created at: 2018-12-08 13:03:51 UTC  
> Updated at: 2020-10-20 05:51:34 UTC  
> Closed at: 2019-01-04 03:20:54 UTC  
> Url: https://github.com/boostorg/random/issues/49  

$ printf "#include <boost/random/mersenne_twister.hpp>\n" | gcc -c -x c++ - -o /dev/null -I ~/pro/build/deps/boost-git/  
    In file included from /home/dscharrer/pro/build/deps/boost-git/boost/random/detail/integer_log2.hpp:19,  
                     from /home/dscharrer/pro/build/deps/boost-git/boost/random/detail/large_arithmetic.hpp:19,  
                     from /home/dscharrer/pro/build/deps/boost-git/boost/random/detail/const_mod.hpp:23,  
                     from /home/dscharrer/pro/build/deps/boost-git/boost/random/detail/seed_impl.hpp:26,  
                     from /home/dscharrer/pro/build/deps/boost-git/boost/random/mersenne_twister.hpp:30,  
                     from <stdin>:1:  
    /home/dscharrer/pro/build/deps/boost-git/boost/pending/integer_log2.hpp:7:59: note: #pragma message: This header is deprecated. Use <boost/integer/integer_log2.hpp> instead.  
     BOOST_HEADER_DEPRECATED("<boost/integer/integer_log2.hpp>");  
                                                               ^

---

## Comment 1

> Username: kersson  
> Created at: 2018-12-19 02:13:05 UTC  
> Updated at: 2018-12-31 02:07:19 UTC  
> Url: https://github.com/boostorg/random/issues/49#issuecomment-448444168  

This is fixed on `develop` by d217e56535816d6b9d662638674939ef205c57e4, but is unfortunately not in [1.69.0](https://github.com/boostorg/random/tree/boost-1.69.0). Do we have to live with these annoying messages until 1.70.0?

---

## Comment 2

> Username: pbertoni89  
> Created at: 2018-12-28 09:43:05 UTC  
> Url: https://github.com/boostorg/random/issues/49#issuecomment-450328863  

@kersson what about prepending  
```  
#define BOOST_PENDING_INTEGER_LOG2_HPP  
#include <boost/integer/integer_log2.hpp>  
```  
to your offending header inclusion, instead of being choosy ?

---

## Comment 3

> Username: kersson  
> Created at: 2018-12-31 02:16:48 UTC  
> Url: https://github.com/boostorg/random/issues/49#issuecomment-450601746  

Thanks for the suggestion @pbertoni. We get these warnings in a number of source files, so we opted to use `BOOST_ALLOW_DEPRECATED_HEADERS` for now.

---

## Comment 4

> Username: dscharrer  
> Created at: 2019-01-04 03:20:54 UTC  
> Url: https://github.com/boostorg/random/issues/49#issuecomment-451344449  

As mentioned by @kersson this has been fixed for Boost 1.70 and there are workarounds to suppress the warnings in 1.69 so I consider this fixed.

---

## Comment 5

> Username: NewCTwo  
> Created at: 2019-01-26 13:43:28 UTC  
> Url: https://github.com/boostorg/random/issues/49#issuecomment-457832200  

Hey, I'm having trouble compiling Caffe (using cmake) with this exact error:  
  
    [ 32%] Building NVCC (Device) object src/caffe/CMakeFiles/cuda_compile_1.dir/util/cuda_compile_1_generated_im2col.cu.o  
    Scanning dependencies of target caffe  
    [ 34%] Building CXX object src/caffe/CMakeFiles/caffe.dir/blob.cpp.o  
    [ 34%] Building CXX object src/caffe/CMakeFiles/caffe.dir/common.cpp.o  
    In file included from /usr/include/boost/random/detail/integer_log2.hpp:19:0,  
                     from /usr/include/boost/random/detail/large_arithmetic.hpp:19,  
                     from /usr/include/boost/random/detail/const_mod.hpp:23,  
                     from /usr/include/boost/random/detail/seed_impl.hpp:26,  
                     from /usr/include/boost/random/mersenne_twister.hpp:30,  
                     from /home/me/Packages/caffe/include/caffe/util/rng.hpp:7,  
                     from /home/me/Packages/caffe/src/caffe/common.cpp:8:  
    /usr/include/boost/pending/integer_log2.hpp:7:89: note: #pragma message: This header is deprecated. Use <boost/integer/integer_log2.hpp> instead.  
     BOOST_HEADER_DEPRECATED("<boost/integer/integer_log2.hpp>");  
                                                                                             ^  
    /home/me/Packages/caffe/src/caffe/common.cpp: In function ‘void caffe::GlobalInit(int*, char***)’:  
    /home/me/Packages/caffe/src/caffe/common.cpp:45:5: error: ‘::gflags’ has not been declared  
       ::gflags::ParseCommandLineFlags(pargc, pargv, true);  
         ^  
    src/caffe/CMakeFiles/caffe.dir/build.make:527: recipe for target 'src/caffe/CMakeFiles/caffe.dir/common.cpp.o' failed  
    make[2]: *** [src/caffe/CMakeFiles/caffe.dir/common.cpp.o] Error 1  
    CMakeFiles/Makefile2:267: recipe for target 'src/caffe/CMakeFiles/caffe.dir/all' failed  
    make[1]: *** [src/caffe/CMakeFiles/caffe.dir/all] Error 2  
    Makefile:129: recipe for target 'all' failed  
    make: *** [all] Error 2  
       
  
How do I enable `BOOST_ALLOW_DEPRECATED_HEADERS` as @kersson suggested?

---

## Comment 6

> Username: NewCTwo  
> Created at: 2019-01-26 13:44:30 UTC  
> Url: https://github.com/boostorg/random/issues/49#issuecomment-457832282  

Boost 1.70 isn't out yet.
