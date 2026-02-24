# #51 - Cannot compile Caffe (using cmake) with this exact error: [Closed]

> Username: NewCTwo  
> Created at: 2019-01-26 16:12:45 UTC  
> Updated at: 2019-01-26 17:52:49 UTC  
> Closed at: 2019-01-26 17:52:49 UTC  
> Url: https://github.com/boostorg/random/issues/51  

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
  
_Originally posted by @NewCTwo in https://github.com/boostorg/random/issues/49#issuecomment-457832200_  
  
I can't wait several months for Boost 1.7 to be released.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-01-26 17:52:49 UTC  
> Url: https://github.com/boostorg/random/issues/51#issuecomment-457851109  

>How do I enable BOOST_ALLOW_DEPRECATED_HEADERS as @kersson suggested?  
  
You add -DBOOST_ALLOW_DEPRECATED_HEADERS to the compilers command line.  No idea how you do that in CMake, sorry!  
But in any case that only suppresses the warning - your actual error does not come from Boost, nor does it name a Boost type:  
  
```  
/home/me/Packages/caffe/src/caffe/common.cpp:45:5: error: ‘::gflags’ has not been declared  
   ::gflags::ParseCommandLineFlags(pargc, pargv, true);  
```  
  
A quick google shows that this is a known caffe issue: https://github.com/BVLC/caffe/issues/2597
