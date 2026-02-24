# #176 [MSVC] problem with BOOST_FUSION_DEFINE_STRUCT_INLINE [Closed]

> Username: hia3  
> Created at: 2016-03-16 23:59:36 UTC  
> Updated at: 2017-12-27 16:14:59 UTC  
> Closed at: 2017-12-27 16:14:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/176  

There is a bug in MSVC that it will call non-explicit constructor of a destination type instead of calling type conversion operator of source class.  
  
BOOST_FUSION_DEFINE_STRUCT_INLINE generates a class that has non-explicit template constructor calling boost::fusion::copy. MSVC instantianes it and fails in boost::fusion::copy because boost::value_initialized<T> is not a sequence.

---

## Comment 1

> Username: daminetreg  
> Created_at: 2016-03-17 11:08:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/176#issuecomment-197825239  

Making explicit default constructors in BOOST_FUSION_DEFINE_STRUCT\* wouldn't solve the issue better ?

---

## Comment 2

> Username: hia3  
> Created_at: 2016-03-17 15:19:55 UTC  
> Updated_at: 2016-03-17 15:21:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/176#issuecomment-197928994  

Making [this constructor](https://github.com/boostorg/fusion/blob/develop/include/boost/fusion/adapted/struct/detail/define_struct_inline.hpp#L442) explicit will solve the problem. Even though making constructors explicit is a good thing in general, I have no idea if is better in this case.

---

## Comment 3

> Username: daminetreg  
> Created_at: 2016-03-17 16:34:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/176#issuecomment-197962935  

Oh this one has to stay implicit, as it allows implicit conversion. One that however be explicit is https://github.com/boostorg/fusion/blob/develop/include/boost/fusion/adapted/struct/detail/define_struct_inline.hpp#L427, but this won't solve your problem in this case.  
  
So sorry for disturbing there. :smile:

---

## Comment 4

> Username: hia3  
> Created_at: 2016-03-17 16:48:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/176#issuecomment-197969981  

Okay, how about enable_if-ing it?  
  
```  
    template <typename boost_fusion_detail_Seq, typename e = boost::enable_if<typename boost::fusion::traits::is_sequence<boost_fusion_detail_Seq>::type, void>::type>                                 \  
    BOOST_CXX14_CONSTEXPR BOOST_FUSION_GPU_ENABLED                              \  
    NAME(const boost_fusion_detail_Seq& rhs)                                    \  
    {                                                                           \  
        boost::fusion::copy(rhs, *this);                                        \  
    }                                                                           \  
  
```

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-12-13 21:34:48 UTC  
> Updated_at: 2017-12-13 21:35:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/176#issuecomment-351532233  

All tests pass on MSVC 2015/2017.  
Is this still an issue?

---
