# #159 Update configuration logic to account for deprecation of <codecvt>. [Merged]

> Username: jzmaddock  
> Created at: 2021-08-19 15:38:37 UTC  
> Updated at: 2021-08-20 13:05:42 UTC  
> Merged at: 2021-08-19 17:18:21 UTC  
> Closed at: 2021-08-19 17:18:21 UTC  
> Url: https://github.com/boostorg/log/pull/159  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-08-19 15:44:48 UTC  
> Url: https://github.com/boostorg/log/pull/159#issuecomment-902023009  

This PR addresses a slight change in the meaning of BOOST_NO_CXX11_HDR_CODECVT in that `<codecvt>` is deprecated from C++17 onwards, and by default msvc emits a hard error if you use any of it's facilities, so BOOST_NO_CXX11_HDR_CODECVT is now set for msvc in C++17 or later.  At the same time, the original bug (missing std::codecvt externals) has been fixed in recent msvc fixes.  
  
There remains just one msvc test failure in C++20 mode which is unrelated to this PR:  
  
```  
D:\data\boost\boost\libs\log\example\bounded_async_log\main.cpp(91,40): error C2672: 'boost::log::v2s_mt_nt6::make_attr_ordering': no matching overloaded function found  
1>D:\data\boost\boost\libs\log\example\bounded_async_log\main.cpp(91,98): error C2893: Failed to specialize function template 'aux::make_attr_ordering_type<FunT,boost::enable_if_c<boost::log::v2s_mt_nt6::aux::arity_of<FunT>::value==2,void>::type,aux::first_argument_type_of<FunT>::type,aux::second_argument_type_of<FunT>::type,boost::enable_if_c<boost::is_same<Arg1T,Arg2T>::value,void>::type>::type boost::log::v2s_mt_nt6::make_attr_ordering(const boost::log::v2s_mt_nt6::attribute_name &,const FunT &)'  
1>        with  
1>        [  
1>            Arg1T=aux::first_argument_type_of<FunT>::type,  
1>            Arg2T=aux::second_argument_type_of<FunT>::type  
1>        ]  
1>d:\data\boost\boost\boost\log\utility\record_ordering.hpp(215): message : see declaration of 'boost::log::v2s_mt_nt6::make_attr_ordering'  
1>D:\data\boost\boost\libs\log\example\bounded_async_log\main.cpp(91,98): message : With the following template arguments:  
1>D:\data\boost\boost\libs\log\example\bounded_async_log\main.cpp(91,98): message : 'FunT=std::less<unsigned int>'  
1>D:\data\boost\boost\libs\log\example\bounded_async_log\main.cpp(91,40): error C2783: 'boost::log::v2s_mt_nt6::attribute_value_ordering<ValueT,FunT> boost::log::v2s_mt_nt6::make_attr_ordering(const boost::log::v2s_mt_nt6::attribute_name &,const FunT &)': could not deduce template argument for 'ValueT'  
1>d:\data\boost\boost\boost\log\utility\record_ordering.hpp(186): message : see declaration of 'boost::log::v2s_mt_nt6::make_attr_ordering'  
```  
  
This is caused by `std::less` having it's member typedefs deprecated from C++17 and removed in C++20, but I haven't traced through your traits class logic to spot what needs to change.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-08-19 15:51:07 UTC  
> Url: https://github.com/boostorg/log/pull/159#issuecomment-902027647  

See also discussion in https://github.com/boostorg/config/issues/391

---

## Comment 3

> Username: Lastique  
> Created_at: 2021-08-19 16:02:34 UTC  
> Url: https://github.com/boostorg/log/pull/159#issuecomment-902036036  

If I'm not mistaken, this makes the macro not defined only on MSVC, while with other compilers it is still defined in C++17 onwards? This doesn't look right.

---

## Comment 4

> Username: Xerus2  
> Created_at: 2021-08-19 16:23:30 UTC  
> Url: https://github.com/boostorg/log/pull/159#issuecomment-902053777  

See also, my ticket here: https://github.com/boostorg/log/issues/160

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-08-19 17:04:17 UTC  
> Url: https://github.com/boostorg/log/pull/159#issuecomment-902086453  

The intent (and I believe what we have) is:  
  
* If this is pre-c++17 and there is no `<codecvt>` then we can check BOOST_NO_CXX11_HDR_CODECVT to see if `std::codecvt` has the required specializations.  
* Post C++17, `<codecvt>` is deprecated and it's use may be an error, so we can't check BOOST_NO_CXX11_HDR_CODECVT for this, and we have to work around each broken compiler/std lib individually.  
* Fortunately, most compilers recent enough to implement C++17 do have the codecvt specializations.  
* The one known exception is msvc-14.1, which is handled as a special case.  
  
I hope that makes sense.

---

## Comment 6

> Username: Lastique  
> Created_at: 2021-08-19 17:17:18 UTC  
> Url: https://github.com/boostorg/log/pull/159#issuecomment-902095265  

Ok, I see, thanks.

---

## Review 7 [Commented]

> Username: Xerus2  
> Created_at: 2021-08-20 00:56:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/log/pull/159#pullrequestreview-734581675  

📁 include/boost/log/detail/config.hpp

```diff
 100 | 
 101 |- #if defined(BOOST_NO_CXX11_HDR_CODECVT)
 101 |+ #if (defined(BOOST_NO_CXX11_HDR_CODECVT) && (BOOST_CXX_VERSION < 201700)) || (defined(_MSVC_STL_VERSION) && (_MSVC_STL_VERSION < 142))
```

> Username: Xerus2  
> Created_at: 2021-08-20 00:56:59 UTC  
> Url: https://github.com/boostorg/log/pull/159#discussion_r692584254  

This didn't work when I tried this change in my local Boost install (I'm pinned against Boost 1.72 for now). I had to change this to:  
#if (defined(BOOST_NO_CXX11_HDR_CODECVT) && (_MSVC_LANG < 201703)) || (defined(_MSVC_STL_VERSION) && (_MSVC_STL_VERSION < 142))  
  
In order for BOOST_LOG_NO_CXX11_CODECVT_FACETS to not be defined.  
The correct version number of _MSVC_LANG to check against for C++17 is 201703, not 201700, and in my case, BOOST_CXX_VERSION was not defined, so I changed it to look at _MSVC_LANG, instead.

> Username: Lastique  
> Created_at: 2021-08-20 08:26:44 UTC  
> Updated_at: 2021-08-20 08:26:45 UTC  
> Url: https://github.com/boostorg/log/pull/159#discussion_r692765491  

I've updated the version check, thanks.


---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-08-20 08:44:00 UTC  
> Url: https://github.com/boostorg/log/pull/159#issuecomment-902536513  

> This didn't work when I tried this change in my local Boost install (I'm pinned against Boost 1.72 for now). I had to change this to:  
>#if (defined(BOOST_NO_CXX11_HDR_CODECVT) && (_MSVC_LANG < 201703)) || (defined(_MSVC_STL_VERSION) && (_MSVC_STL_VERSION < 142))  
  
You will need the most recent Boost release to have BOOST_CXX_VERSION: BOOST_CXX_VERSION is the correct check here, this is NOT necessarily just an MSVC issue.  Once `<codecvt>` is removed by the gnu and llvm std libraries (C++2b I assume), then they will need the check also, hence the generic test, rather than an MSVC specific one.  
  
>In order for BOOST_LOG_NO_CXX11_CODECVT_FACETS to not be defined.  
>The correct version number of _MSVC_LANG to check against for C++17 is 201703, not 201700, and in my case, >BOOST_CXX_VERSION was not defined, so I changed it to look at _MSVC_LANG, instead.  
  
I was sloppy with the version number, though it makes no practical difference IMO ;)

---

## Comment 9

> Username: Xerus2  
> Created_at: 2021-08-20 13:05:41 UTC  
> Url: https://github.com/boostorg/log/pull/159#issuecomment-902678989  

Got it, thanks. I will take a note to switch my Boost install to look for BOOST_CXX_VERSION once it becomes available; I should have access to Boost 1.75 soon.

---
