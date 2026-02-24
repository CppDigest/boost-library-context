# #274 - problem with pct_encoded_view::operator== [Closed]

> Username: vinniefalco  
> Created at: 2022-07-29 01:31:49 UTC  
> Updated at: 2022-07-30 02:19:13 UTC  
> Closed at: 2022-07-30 02:19:13 UTC  
> Url: https://github.com/boostorg/url/issues/274  

I have a weird bug only in clang OSX. The CI failure:  
  
https://drone.cpp.al/CPPAlliance/url/728/42/2  
  
the branch:  
  
https://github.com/vinniefalco/url/tree/std-equal  
  
the error:  
  
```  
/Applications/Xcode-12.3.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/algorithm:1262:14: error: call to object of type 'std::__1::__equal_to<boost::urls::query_param_view, boost::urls::query_param>' is ambiguous  
        if (!__pred(*__first1, *__first2))  
             ^~~~~~  
/Applications/Xcode-12.3.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/algorithm:1275:19: note: in instantiation of function template specialization 'std::__1::equal<const boost::urls::query_param_view *, boost::urls::params_encoded_view::iterator, std::__1::__equal_to<boost::urls::query_param_view, boost::urls::query_param> >' requested here  
    return _VSTD::equal(__first1, __last1, __first2, __equal_to<__v1, __v2>());  
                  ^  
libs/url/test/unit/rfc/query_rule.cpp:53:25: note: in instantiation of function template specialization 'std::__1::equal<const boost::urls::query_param_view *, boost::urls::params_encoded_view::iterator>' requested here  
        BOOST_TEST(std::equal(  
                        ^  
/Applications/Xcode-12.3.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/algorithm:668:66: note: candidate function  
    _LIBCPP_INLINE_VISIBILITY _LIBCPP_CONSTEXPR_AFTER_CXX11 bool operator()(const _T1& __x, const _T1& __y) const {return __x == __y;}  
                                                                 ^  
/Applications/Xcode-12.3.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/algorithm:669:66: note: candidate function  
    _LIBCPP_INLINE_VISIBILITY _LIBCPP_CONSTEXPR_AFTER_CXX11 bool operator()(const _T1& __x, const _T2& __y) const {return __x == __y;}  
                                                                 ^  
1 error generated.  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-07-29 14:17:41 UTC  
> Url: https://github.com/boostorg/url/issues/274#issuecomment-1199366982  

I've been through this error before with the examples. Many implementations of equal_to are not transparent so they fail. I wrote my own equal to in the example.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-29 15:17:30 UTC  
> Url: https://github.com/boostorg/url/issues/274#issuecomment-1199511786  

Really? But why just this ONE specific version of clang and literally nothing else? Can I fix this by adding is_transparent to something? Having to write is_equal over and over again is kind of annoying

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-07-29 15:22:55 UTC  
> Url: https://github.com/boostorg/url/issues/274#issuecomment-1199523249  

I'll have a look

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-07-29 15:27:07 UTC  
> Url: https://github.com/boostorg/url/issues/274#issuecomment-1199533312  

My concern is our conversions to and from string_view, pct_encoded_view, and such. Perhaps some compiler bug or some situation we didn't think of.
