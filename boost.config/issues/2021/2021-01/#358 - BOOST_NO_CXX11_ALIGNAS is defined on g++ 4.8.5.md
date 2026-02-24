# #358 - BOOST_NO_CXX11_ALIGNAS is defined on g++ 4.8.5... [Closed]

> Username: pdimov  
> Created at: 2021-01-25 19:49:53 UTC  
> Updated at: 2021-02-25 16:55:57 UTC  
> Closed at: 2021-02-25 16:55:57 UTC  
> Url: https://github.com/boostorg/config/issues/358  

... but it's not clear why; the test https://github.com/boostorg/config/blob/develop/test/boost_no_cxx11_alignas.ipp passes on 4.8.1 .. 4.8.5: https://godbolt.org/z/jGT1xK https://godbolt.org/z/jqnYEE  
  
https://github.com/boostorg/config/blob/8a4ef2c2f3d8bb61a0d9c938d9ae1cb046f76f63/include/boost/config/compiler/gcc.hpp#L272-L276 says that `alignas` doesn't accept constant expressions, but the test uses the constant expression `alignment< 16u >::value` and that works.  
  
Either the macro shouldn't be defined, or if not defining it causes failures, the test should be extended with the failing case.  
  
This is relevant because Boost.Json uses `cxx11_alignas` as a build requirement and users are reporting failure to build on CentOS 7 which uses g++ 4.8.5.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-01-26 08:39:18 UTC  
> Url: https://github.com/boostorg/config/issues/358#issuecomment-767390551  

The commit that changed this is: https://github.com/boostorg/config/commit/c3f81f3c7024ab5428859b8fa9cdf256c73c93ee  
  
@Lastique ?

---

## Comment 2

> Username: Lastique  
> Created at: 2021-01-26 09:32:17 UTC  
> Url: https://github.com/boostorg/config/issues/358#issuecomment-767419103  

The compiler does not support `alignas` for constant expressions (more elaborate than just literals). This affected Boost.Atomic and was discovered by tests, hence my PR.  
  
https://godbolt.org/z/xvPjoz  
  
The test should probably be updated.
