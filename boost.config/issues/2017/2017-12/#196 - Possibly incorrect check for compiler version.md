# #196 - Possibly incorrect check for compiler version [Closed]

> Username: jcageman  
> Created at: 2017-12-06 10:05:14 UTC  
> Updated at: 2017-12-09 18:11:00 UTC  
> Closed at: 2017-12-09 18:11:00 UTC  
> Url: https://github.com/boostorg/config/issues/196  

The below > comparison seems to be incorrect according to the comments within the file [visualc.hpp  
](https://github.com/boostorg/config/blob/develop/include/boost/config/compiler/visualc.hpp)  
```  
//  
// last known and checked version is 19.11.25506 (VC++ 2017.3):  
#if (_MSC_VER > 1911)  
#  if defined(BOOST_ASSERT_CONFIG)  
#     error "Boost.Config is older than your current compiler version."  
#  elif !defined(BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE)  
#     pragma message("Info: Boost.Config is older than your compiler version - probably nothing bad will happen - but you may wish to look for an updated Boost version. Define BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE to suppress this message.")  
#  endif  
#endif  
```  
  
According to  
  
```  
//  Microsoft Visual C++ compiler setup:  
//  
//  We need to be careful with the checks in this file, as contrary  
//  to popular belief there are versions with _MSC_VER with the final  
//  digit non-zero (mainly the MIPS cross compiler).  
//  
//  So we either test _MSC_VER >= XXXX or else _MSC_VER < XXXX.  
//  No other comparisons (==, >, or <=) are safe.  
```  
I stumbled on this due to the error given about an outdated compiler version.

---

## Comment 1

> Username: eldiener  
> Created at: 2017-12-09 12:59:13 UTC  
> Url: https://github.com/boostorg/config/issues/196#issuecomment-350461936  

This is just an effort to tell the end-user that the vc++ compiler version is newer than the latest version which Boost supports. So I think that using '>' is justified in this case.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2017-12-09 18:11:00 UTC  
> Url: https://github.com/boostorg/config/issues/196#issuecomment-350495096  

Please see: https://github.com/boostorg/config/pull/198
