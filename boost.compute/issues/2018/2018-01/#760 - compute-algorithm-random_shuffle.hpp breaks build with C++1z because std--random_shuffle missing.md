# #760 - compute/algorithm/random_shuffle.hpp breaks build with C++1z because std::random_shuffle missing [Closed]

> Username: jeffhammond  
> Created at: 2018-01-22 15:55:17 UTC  
> Updated at: 2018-03-10 08:24:02 UTC  
> Closed at: 2018-03-10 08:24:01 UTC  
> Url: https://github.com/boostorg/compute/issues/760  

LLVM's libc++ has implemented the [deprecation and removal](http://en.cppreference.com/w/cpp/algorithm/random_shuffle) of `std::random_shuffle( RandomIt first, RandomIt last );`, which is used unconditionally in compute/algorithm/random_shuffle.hpp.   
 https://github.com/llvm-mirror/libcxx/blob/master/include/algorithm#L3043 has details.  
  
There is a straightforward fix involving the use of `std::shuffle`, which was introduced in C++11.  If this change is made, should it be guarded by BOOST_COMPUTE_USE_CPP11 or should it use something associated with C++14/17 so as to avoid breaking backwards-compatibility (of the pseudo-randomization) for C++11 (and possibly C++14) users?  
  
Also, do you have a preference on which [PRNG](http://en.cppreference.com/w/cpp/numeric/random) to use?  I used `std::default_random_engine` in my local modification, but that is the least reproducible, given that it is implementation-defined.  
  
I'll work on the fix once I know in which direction to go.  
  
# Details  
```  
/usr/local/Cellar/llvm/5.0.1/bin/clang++ -std=c++1z -pthread -g -O3 -mtune=native -ffast-math -DPRKVERSION="2.16" stencil-vector-pstl.cc  -DUSE_BOOST -I/usr/local/Cellar/boost/1.65.1/include -o stencil-vector-stl  
In file included from stencil-vector-pstl.cc:63:  
In file included from ./prk_util.h:185:  
In file included from /usr/local/include/boost/compute.hpp:14:  
In file included from /usr/local/include/boost/compute/algorithm.hpp:64:  
/usr/local/include/boost/compute/algorithm/random_shuffle.hpp:49:5: error: no member named 'random_shuffle' in namespace 'std';  
      did you mean simply 'random_shuffle'?  
    std::random_shuffle(random_indices.begin(), random_indices.end());  
    ^~~~~  
/usr/local/include/boost/compute/algorithm/random_shuffle.hpp:35:13: note: 'random_shuffle' declared here  
inline void random_shuffle(Iterator first,  
            ^  
1 error generated.  
make: *** [stencil-vector-stl] Error 1  
```  
  
It works fine with `-std=c++14`.

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-01-22 20:34:14 UTC  
> Url: https://github.com/boostorg/compute/issues/760#issuecomment-359555861  

> There is a straightforward fix involving the use of std::shuffle, which was introduced in C++11. If this change is made, should it be guarded by BOOST_COMPUTE_USE_CPP11 or should it use something associated with C++14/17 so as to avoid breaking backwards-compatibility (of the pseudo-randomization) for C++11 (and possibly C++14) users?  
  
I think your change is good. Thanks for it!  
  
> Also, do you have a preference on which PRNG to use? I used std::default_random_engine in my local modification, but that is the least reproducible, given that it is implementation-defined.  
  
IMHO, it's a good choice.

---

## Comment 2

> Username: jszuppe  
> Created at: 2018-03-10 08:24:01 UTC  
> Url: https://github.com/boostorg/compute/issues/760#issuecomment-372012823  

Fixed in #761
