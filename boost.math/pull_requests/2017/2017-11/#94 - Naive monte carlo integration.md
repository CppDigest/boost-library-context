# #94 Naive monte carlo integration [Merged]

> Username: NAThompson  
> Created at: 2017-11-22 18:10:33 UTC  
> Updated at: 2018-02-20 17:14:43 UTC  
> Merged at: 2018-02-20 08:11:01 UTC  
> Closed at: 2018-02-20 08:11:01 UTC  
> Url: https://github.com/boostorg/math/pull/94  

This is a work in progress, but still pretty good. It needs to handle exceptions in threads and I think it would be nice to support infinite domains, but those are arguably solvable problems.  
  
Check it out, let me know what y'all think.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-02-17 17:51:40 UTC  
> Url: https://github.com/boostorg/math/pull/94#issuecomment-366458726  

Nick, I'm having difficulty getting the tests clean with this one - ever time I think I have it something new pops up, see for example: https://travis-ci.org/boostorg/math/jobs/342688832  
  
```  
testing.capture-output ../../../bin.v2/libs/math/test/naive_monte_carlo_test_10.test/gcc-6.3.0/debug/link-static/naive_monte_carlo_test_10.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Testing that double infinite bounds are integrated correctly by naive Monte-Carlo on type float  
naive_monte_carlo_test.cpp(277): error: in "naive_monte_carlo_test": difference{-nan} between y{-nan} and boost::math::constants::pi<Real>(){3.14159274} exceeds 0.01  
Testing that double infinite bounds are integrated correctly by naive Monte-Carlo on type double  
Testing that the Radovic function is integrated correctly by naive Monte-Carlo on type float  
*** 1 failure is detected in the test module "naive_monte_carlo_test"  
```  
  
But there are others, mostly it's the infinite bounds tests that fail though... reproduction is next to impossible though because it is quite literally "random".  My guess is that the generators are creating values too close to the endpoints on some runs but not others.  Any ideas?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-02-17 18:02:24 UTC  
> Url: https://github.com/boostorg/math/pull/94#issuecomment-366459601  

There are two things I hope to push today:  
  
1) An option to pass a seed. Without this, the tests will sporadically fail forever. The error bound is simply a 1 std deviation error bar, so I made the test roughly 3 std deviations above the error bar. But this just changes the problem.  
2) The double infinite bounds do have a problem: The transform maps from (-inf, inf) -> (0, 1), but in fact the transform should map from (-eps, 1+eps) (maybe a bit closer to 0 than -eps). The half infinite and bounded domains should never hit singularities on the boundary; I think they are correct.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2018-02-17 23:48:50 UTC  
> Url: https://github.com/boostorg/math/pull/94#issuecomment-366480254  

Ok, now it supports a user-provided seed. This is still not fully deterministic, because the routine "observes" the variance every 100ms, but I think this will be sufficient to make failures very rare. All transforms should work now, unless I *still* need to increase the sophistication of my mental model of floating point arithmetic.  . . Presumably that's a significant caveat.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-02-18 18:35:52 UTC  
> Url: https://github.com/boostorg/math/pull/94#issuecomment-366536770  

OK, much closer, but there are still a couple of outstanding issues: over large object file sizes with msvc-14.0 (which I'll take care of), and some failures with older std lib versions (GCC and msvc) and all versions of clang/libc++, the typical error message is:  
  
```  
clang-darwin.compile.c++ ../../../bin.v2/libs/math/test/naive_monte_carlo_test_1.test/clang-darwin-4.2.1/debug/link-static/naive_monte_carlo_test.o  
In file included from naive_monte_carlo_test.cpp:9:  
In file included from /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/ostream:138:  
In file included from /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/ios:216:  
In file included from /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/__locale:15:  
In file included from /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/string:439:  
In file included from /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/algorithm:628:  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/memory:1752:31: error: no matching constructor for initialization of 'std::__1::pair<const unsigned long, boost::atomics::atomic<double> >'  
            ::new((void*)__p) _Up(_VSTD::forward<_Args>(__args)...);  
                              ^   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/memory:1668:18: note: in instantiation of function template specialization 'std::__1::allocator<std::__1::__tree_node<std::__1::__value_type<unsigned long, boost::atomics::atomic<double> >, void *> >::construct<std::__1::pair<const unsigned long, boost::atomics::atomic<double> >, unsigned long &, double &>' requested here  
            {__a.construct(__p, _VSTD::forward<_Args>(__args)...);}  
                 ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/memory:1514:14: note: in instantiation of function template specialization 'std::__1::allocator_traits<std::__1::allocator<std::__1::__tree_node<std::__1::__value_type<unsigned long, boost::atomics::atomic<double> >, void *> > >::__construct<std::__1::pair<const unsigned long, boost::atomics::atomic<double> >, unsigned long &, double &>' requested here  
            {__construct(__has_construct<allocator_type, _Tp*, _Args...>(),  
             ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/__tree:2152:20: note: in instantiation of function template specialization 'std::__1::allocator_traits<std::__1::allocator<std::__1::__tree_node<std::__1::__value_type<unsigned long, boost::atomics::atomic<double> >, void *> > >::construct<std::__1::pair<const unsigned long, boost::atomics::atomic<double> >, unsigned long &, double &>' requested here  
    __node_traits::construct(__na, _NodeTypes::__get_ptr(__h->__value_), _VSTD::forward<_Args>(__args)...);  
                   ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/__tree:2097:29: note: in instantiation of function template specialization 'std::__1::__tree<std::__1::__value_type<unsigned long, boost::atomics::atomic<double> >, std::__1::__map_value_compare<unsigned long, std::__1::__value_type<unsigned long, boost::atomics::atomic<double> >, std::__1::less<unsigned long>, true>, std::__1::allocator<std::__1::__value_type<unsigned long, boost::atomics::atomic<double> > > >::__construct_node<unsigned long &, double &>' requested here  
        __node_holder __h = __construct_node(_VSTD::forward<_Args>(__args)...);  
                            ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/__tree:1157:16: note: in instantiation of function template specialization 'std::__1::__tree<std::__1::__value_type<unsigned long, boost::atomics::atomic<double> >, std::__1::__map_value_compare<unsigned long, std::__1::__value_type<unsigned long, boost::atomics::atomic<double> >, std::__1::less<unsigned long>, true>, std::__1::allocator<std::__1::__value_type<unsigned long, boost::atomics::atomic<double> > > >::__emplace_unique_key_args<unsigned long, unsigned long &, double &>' requested here  
        return __emplace_unique_key_args(__f, _VSTD::forward<_First>(__f),  
               ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/map:1044:24: note: in instantiation of function template specialization 'std::__1::__tree<std::__1::__value_type<unsigned long, boost::atomics::atomic<double> >, std::__1::__map_value_compare<unsigned long, std::__1::__value_type<unsigned long, boost::atomics::atomic<double> >, std::__1::less<unsigned long>, true>, std::__1::allocator<std::__1::__value_type<unsigned long, boost::atomics::atomic<double> > > >::__emplace_unique<unsigned long &, double &>' requested here  
        return __tree_.__emplace_unique(_VSTD::forward<_Args>(__args)...);  
                       ^  
../../../boost/math/quadrature/naive_monte_carlo.hpp:172:31: note: in instantiation of function template specialization 'std::__1::map<unsigned long, boost::atomics::atomic<double>, std::__1::less<unsigned long>, std::__1::allocator<std::__1::pair<const unsigned long, boost::atomics::atomic<double> > > >::emplace<unsigned long &, double &>' requested here  
            m_thread_averages.emplace(i, y);  
                              ^  
naive_monte_carlo_test.cpp:146:42: note: in instantiation of member function 'boost::math::quadrature::naive_monte_carlo<double, (lambda at naive_monte_carlo_test.cpp:139:14), std::__1::mersenne_twister_engine<unsigned long long, 64, 312, 156, 31, 13043109905998158313, 29, 6148914691236517205, 17, 8202884508482404352, 37, 18444473444759240704, 43, 6364136223846793005>, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >::naive_monte_carlo' requested here  
    naive_monte_carlo<Real, decltype(g)> mc(g, bounds, (Real) 0.01, true, 1, 1922);  
                                         ^  
naive_monte_carlo_test.cpp:346:5: note: in instantiation of function template specialization 'test_finite_singular_boundary<double>' requested here  
    test_finite_singular_boundary<double>();  
    ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/utility:273:5: note: candidate constructor not viable: no known conversion from 'double' to 'const boost::atomics::atomic<double>' for 2nd argument  
    pair(const _T1& __x, const _T2& __y)  
    ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/utility:278:9: note: candidate template ignored: could not match 'pair<type-parameter-0-0, type-parameter-0-1>' against 'unsigned long'  
        pair(const pair<_U1, _U2>& __p  
        ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/utility:313:42: note: candidate template ignored: disabled by 'enable_if' [with _U1 = unsigned long &, _U2 = double &]  
              class = typename enable_if<is_convertible<_U1, first_type>::value &&  
                                         ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/utility:323:9: note: candidate template ignored: could not match 'pair<type-parameter-0-0, type-parameter-0-1>' against 'unsigned long'  
        pair(pair<_U1, _U2>&& __p,  
        ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/utility:270:49: note: candidate constructor template not viable: requires 0 arguments, but 2 were provided  
    _LIBCPP_INLINE_VISIBILITY _LIBCPP_CONSTEXPR pair() : first(), second() {}  
                                                ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/utility:357:9: note: candidate constructor template not viable: requires single argument '__p', but 2 arguments were provided  
        pair(_Tuple&& __p)  
        ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/utility:368:9: note: candidate constructor template not viable: requires 3 arguments, but 2 were provided  
        pair(piecewise_construct_t __pc, tuple<_Args1...> __first_args,  
        ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/utility:405:9: note: candidate constructor template not viable: requires 5 arguments, but 2 were provided  
        pair(piecewise_construct_t,  
        ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/utility:288:5: note: candidate constructor not viable: requires single argument '__p', but 2 arguments were provided  
    pair(const pair& __p) = default;  
```  
  
And I don't see an easy way to fix this... as it seems to relate to the particular implementation of the std lib's internals.  When I saw this with msvc-12.0 I thought it was an msvc bug and disabled the tests for that case.  But it also effects gcc-5.x and _**all**_ versions of libc++.  
It would be interesting to know if this is supposed to be supported or not!

---

## Comment 5

> Username: NAThompson  
> Created_at: 2018-02-18 19:11:17 UTC  
> Url: https://github.com/boostorg/math/pull/94#issuecomment-366539316  

[Here's](https://stackoverflow.com/questions/30048533/why-isnt-atomic-double-fully-implemented) a SO answer I think could be of relevance:  
  
> The standard library mandates std::atomic<T> where T is any TriviallyCopyable type. Since double is TriviallyCopyable, std::atomic<double> should compile and work perfectly well.  
  
Since the Monte Carlo integration only does loads and stores, and no arithmetic on atomics, then presumably we should get away with using std::atomic in C++11 mode. I'm not aware if this will fix the problem, but it ostensibly could allow us to #ifdef on compiler versions, using boost::atomic wherever it works and std::atomic elsewhere.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2018-02-18 23:26:28 UTC  
> Url: https://github.com/boostorg/math/pull/94#issuecomment-366557999  

By swapping out boost::atomic with std::atomic, we lose long double support on gcc-7, this is a [known bug](https://gcc.gnu.org/ml/gcc-patches/2017-02/msg00351.html). I suspect this is not a huge issue because recovering long double precision via Monte-Carlo should take the roughly the lifetime of the universe-though admittedly there are other motivations for using higher precision than simply recovering the full precision of the type.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-02-19 18:27:52 UTC  
> Url: https://github.com/boostorg/math/pull/94#issuecomment-366771864  

I have a workaround which works rather well so far - just use std::unique_ptr<atomic<whatever>[]> rather than maps.  Running tests locally before merging, but it works with vc12 which is a good start.  It's actually rather more lightweight than using a map as well, even if it is a circa 1990 solution ;)

---

## Comment 8

> Username: NAThompson  
> Created_at: 2018-02-19 19:49:13 UTC  
> Updated_at: 2018-02-19 19:49:23 UTC  
> Url: https://github.com/boostorg/math/pull/94#issuecomment-366787893  

The maps were themselves a workaround for inability to construct a vector of atomics, so I think yours is a better solution.

---
