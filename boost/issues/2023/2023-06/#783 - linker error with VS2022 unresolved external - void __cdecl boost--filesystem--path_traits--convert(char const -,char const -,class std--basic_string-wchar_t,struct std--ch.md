# #783 - linker error with VS2022 unresolved external - void __cdecl boost::filesystem::path_traits::convert(char const *,char const *,class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > &,class std::codecvt<wchar_t,char,struct _Mbstatet> const &) [Closed]

> Username: pceccato  
> Created at: 2023-06-13 02:23:23 UTC  
> Updated at: 2023-06-13 06:14:36 UTC  
> Closed at: 2023-06-13 06:14:04 UTC  
> Url: https://github.com/boostorg/boost/issues/783  

I get the following linker error when building my projects against boost 1.82:  
  
```  
error LNK2001: unresolved external symbol "void __cdecl boost::filesystem::path_traits::convert(char const *,char const *,class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > &,class std::codecvt<wchar_t,char,struct _Mbstatet> const &)" (?convert@path_traits@filesystem@boost@@YAXPEBD0AEAV?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@AEBV?$codecvt@_WDU_Mbstatet@@@5@@Z)  
1>D:\itslinkcpp\ITSLinkSubscriptionClient\x64\Release\ITSLinkSubscriptionClient.exe : fatal error LNK1120: 1 unresolved externals  
```  
  
The w_char version of convert links properly:  
  
```  
1>      Found "void __cdecl boost::filesystem::detail::path_traits::convert(wchar_t const *,wchar_t const *,class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > &,class std::codecvt<wchar_t,char,struct _Mbstatet> const *)" (?convert@path_traits@detail@filesystem@boost@@YAXPEB_W0AEAV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@PEBV?$codecvt@_WDU_Mbstatet@@@6@@Z)  
1>        Referenced in boost_filesystem-vc140-mt.lib(exception.obj)  
  
```  
  
I did a DUMPBIN /SYMBOLS of boost_filesystem-vc140-mt.lib and noticed that it fails to link because it is looking for a version of convert() where the last argument is a **const &**, but the version in the lib has a const * for the last argument.  
  
I never call convert directly in my code?  
  
Is this a bug, or am I doing it wrong?

---

## Comment 1

> Username: pceccato  
> Created at: 2023-06-13 06:14:36 UTC  
> Url: https://github.com/boostorg/boost/issues/783#issuecomment-1588612716  

a clean and rebuild solved the problem
