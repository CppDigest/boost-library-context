# #134 - Compile fail using Clang 600 missing    mutable boost::math::detail::atomic_unsigned_type [Closed]

> Username: pabristow  
> Created at: 2018-07-07 18:00:47 UTC  
> Updated at: 2018-07-30 17:17:36 UTC  
> Closed at: 2018-07-30 17:17:31 UTC  
> Url: https://github.com/boostorg/math/issues/134  

I:\modular-boost\boost/math/quadrature/detail/tanh_sinh_detail.hpp:174:33: error: no type named 'atomic_unsigned_type' in namespace 'boost::math::detail'  
  
   mutable boost::math::detail::atomic_unsigned_type      m_committed_refinements;  
  
#ifdef BOOST_MATH_NO_ATOMIC_INT  
#error "Ooops"  
#endif  
  
fires, but #undef still fails.  
  
[codeblocks test_lambertw.log](https://github.com/boostorg/math/files/2172956/codeblocks.test_lambertw.log)  
  
(Works for gcc 8.1 and 7.2 and VS)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-07-30 17:17:31 UTC  
> Url: https://github.com/boostorg/math/issues/134#issuecomment-408941338  

This should now be fixed in https://github.com/boostorg/math/commit/9e3bf4e3e19b5e3f8a9bc1726c4586ef644e4d72.
