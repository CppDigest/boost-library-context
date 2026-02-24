# #532 - #pragma warning (push) / #pragma warning (pop) mismatch in escape_char.ipp when compiling with Visual Studio 2017 [Closed]

> Username: stefan301  
> Created at: 2019-09-09 10:54:52 UTC  
> Updated at: 2019-09-20 19:57:33 UTC  
> Closed at: 2019-09-20 19:57:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/532  

In escape_char.ipp (Boost 1.71.0) there is the following block starting at line 36 enabled when compiling with any Visual Studio release:  
  
#if defined(BOOST_MSVC)  
#pragma warning(push)  
#pragma warning(disable:4127)  
#endif  
  
but the block containing the pop starting at line 169 is only enable when compiling with Visual Studio .NET 2003 (7,1) or older:  
  
#if (defined(BOOST_MSVC) && (BOOST_MSVC <= 1310))  
#pragma warning(pop)  
#endif

---

## Comment 1

> Username: djowel  
> Created at: 2019-09-10 22:27:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/532#issuecomment-530144792  

Thanks for noting. Could you do a PR for this?
