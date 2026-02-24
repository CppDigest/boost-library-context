# #300 - 'iterator_range' in namespace 'boost::mpl' does not name a template type [Closed]

> Username: NAThompson  
> Created at: 2021-02-24 15:36:32 UTC  
> Updated at: 2021-02-24 18:47:07 UTC  
> Closed at: 2021-02-24 18:47:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/300  

Just found this in math:  
  
```  
math/test$ make wavelet_transform_test.x  
g++-10 -g -Wall -Wextra --std=gnu++17 -mfma -fno-finite-math-only -march=native -Wfatal-errors -MMD -O3 -I../../../ -I/usr/local/include -o wavelet_transform_test.x wavelet_transform_test.cpp -L/usr/local/lib -pthread  -lfftw3f -lfftw3 -lfftw3l -lquadmath  
In file included from ../../../boost/multiprecision/detail/generic_interconvert.hpp:9,  
                 from ../../../boost/multiprecision/number.hpp:26,  
                 from ../../../boost/multiprecision/float128.hpp:12,  
                 from wavelet_transform_test.cpp:22:  
../../../boost/multiprecision/detail/default_ops.hpp:910:17: error: 'iterator_range' in namespace 'boost::mpl' does not name a template type  
  910 |    typedef mpl::iterator_range<start_seq, typename mpl::end<list_type>::type> range;  
      |                 ^~~~~~~~~~~~~~  
compilation terminated due to -Wfatal-errors.  
make: *** [wavelet_transform_test.x] Error 1  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-02-24 18:17:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/300#issuecomment-785274553  

Looks like your multiprecision isn't up to date with develop?

---

## Comment 2

> Username: NAThompson  
> Created at: 2021-02-24 18:42:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/300#issuecomment-785289814  

D'oh! You're right. Updated and it's cool.
