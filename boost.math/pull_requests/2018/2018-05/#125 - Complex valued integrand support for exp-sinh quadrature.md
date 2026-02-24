# #125 Complex valued integrand support for exp-sinh quadrature [Merged]

> Username: NAThompson  
> Created at: 2018-05-03 04:19:43 UTC  
> Updated at: 2018-05-25 07:10:47 UTC  
> Merged at: 2018-05-25 07:10:23 UTC  
> Closed at: 2018-05-25 07:10:23 UTC  
> Url: https://github.com/boostorg/math/pull/125  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-05-03 13:48:40 UTC  
> Url: https://github.com/boostorg/math/pull/125#issuecomment-386301640  

Well, that bloodbath of a build is totally inexplicable to me. Error of 10^-25? Not consistent with a bad parse, not consistent with a stray long double. Not even close to the 128 bit epsilon of 10^-32.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-05-22 17:01:20 UTC  
> Url: https://github.com/boostorg/math/pull/125#issuecomment-391066060  

The tests with cpp_complex_quad fail when GNU extensions are turn on, and pass when they're off.  So there's a bug in our `__float128` support somewhere, just don't know where/why yet....

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-05-22 17:39:18 UTC  
> Url: https://github.com/boostorg/math/pull/125#issuecomment-391078014  

The bug is in  
  
```  
template<class Real, class Policy>  
void exp_sinh_detail<Real, Policy>::init(const mpl::int_<4>&)  
```  
everything works if I disable it.  But I don't see the error yet...

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-05-23 17:09:26 UTC  
> Url: https://github.com/boostorg/math/pull/125#issuecomment-391426476  

OK, this is nearly fixed: appveyor failures are due to serialization (I've filed a bug report), travis has one failure: https://travis-ci.org/boostorg/math/jobs/382315019  
  
I don't have access to this platform - do you?   I'm not sure if it's a multiprecision or a type_traits issue, or equally likely a Mac-clang bug.  If you have reproduce, can you change number.hpp:642:  
  
```  
#if BOOST_WORKAROUND(BOOST_MSVC, < 1900)  
   template <class T>  
#else  
   template <class T, class = typename boost::disable_if_c<boost::is_constructible<T, self_type const&>::value || !boost::is_default_constructible<T>::value, T>::type>  
#endif  
```  
  
To  
  
```  
#if 1//BOOST_WORKAROUND(BOOST_MSVC, < 1900)  
   template <class T>  
#else  
   template <class T, class = typename boost::disable_if_c<boost::is_constructible<T, self_type const&>::value || !boost::is_default_constructible<T>::value, T>::type>  
#endif  
```  
  
and then also try:  
  
```  
#if BOOST_WORKAROUND(BOOST_MSVC, < 1900)  
   template <class T>  
#else  
   template <class T, class = typename boost::disable_if_c<std::is_constructible<T, self_type const&>::value || !std::is_default_constructible<T>::value, T>::type>  
#endif  
```  
  
Thanks!

---

## Comment 5

> Username: NAThompson  
> Created_at: 2018-05-24 06:59:13 UTC  
> Url: https://github.com/boostorg/math/pull/125#issuecomment-391609982  

Built against boost/multiprecision latest commit (cbbfff8bfd036faef0f03bb2e472abe003a6c4d5), and of course the latest boost.math.  
  
```  
$ clang++ --version  
Apple LLVM version 9.1.0 (clang-902.0.39.1)  
Target: x86_64-apple-darwin17.5.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```  
  
Command line:  
  
```  
$ make  
clang++ --std=c++14 -O2 -Wfatal-errors -I./include -I../multiprecision/include -I../container_hash/include test/test_cubic_b_spline.cpp -o main.x  
```  
  
which is as close as I can get to the config from the failing job log.  
  
On this system, it succeeded without any changes.  
  
When applying the first suggestion:  
  
```  
#if 1//BOOST_WORKAROUND(BOOST_MSVC, < 1900)  
```  
  
it compiled and the test passed.  
  
When applying the second suggestion:  
  
```  
#if BOOST_WORKAROUND(BOOST_MSVC, < 1900)  
   template <class T>  
#else  
   template <class T, class = typename boost::disable_if_c<std::is_constructible<T, self_type const&>::value || !std::is_default_constructible<T>::value, T>::type>  
#endif  
```  
  
again, the compile and test succeeded.  
  
I also did these tests using `b2`, via the command line:  
  
```  
➜  test git:(complex_exp_sinh) ✗ ../../../b2 -j3 -d+0 -q toolset=clang --compiler=clang++ --cxxflags=--std=c++14 misc  
```  
  
with the same results.  
  
I thought maybe the compiler would be "similar enough" to reproduce, but I guess not.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2018-05-24 08:05:33 UTC  
> Url: https://github.com/boostorg/math/pull/125#issuecomment-391626466  

Thanks Nick, good to know it works with clang-9 anyway.  I'll try and test this another way.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2018-05-24 12:41:25 UTC  
> Url: https://github.com/boostorg/math/pull/125#issuecomment-391700043  

BTW, is Apple Clang 8 relevant? I get the compiler auto-updated, so anyone on apple clang 8 is forcefully rejecting updates.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2018-05-24 16:04:15 UTC  
> Url: https://github.com/boostorg/math/pull/125#issuecomment-391769711  

>BTW, is Apple Clang 8 relevant? I get the compiler auto-updated, so anyone on apple clang 8 is forcefully rejecting updates.  
  
Probably not, no.  
  
In any case travis is green now, just waiting on appveyor and then hopefully I can merge.  Will change the CI to apple-9.3 afterwards.

---
