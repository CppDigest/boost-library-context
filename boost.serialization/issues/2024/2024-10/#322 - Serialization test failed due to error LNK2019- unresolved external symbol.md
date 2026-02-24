# #322 - [MSVC] Serialization test failed due to error LNK2019: unresolved external symbol [Open]

> Username: brianGriifin114  
> Created at: 2024-10-11 07:49:53 UTC  
> Updated at: 2025-02-03 13:16:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/322  

Hi , The MSVC testing team uses boost to test compiler issues. Recently we noticed that serialization test are failing .The error message as below:  
```  
A.cpp  
msvc.link C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\serialization\test\test_array_text_archive.test\msvc-14.3\release\x86_64\threading-multi\test_array_text_archive.exe  
   Creating library C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\serialization\test\test_array_text_archive.test\msvc-14.3\release\x86_64\threading-multi\test_array_text_archive.lib and object C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\serialization\test\test_array_text_archive.test\msvc-14.3\release\x86_64\threading-multi\test_array_text_archive.exp  
test_array.obj : error LNK2019: unresolved external symbol "void __cdecl boost::filesystem::detail::path_traits::convert(char const *,char const *,class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > &,class std::codecvt<wchar_t,char,struct _Mbstatet> const *)" (?convert@path_traits@detail@filesystem@boost@@YAXPEBD0AEAV?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@PEBV?$codecvt@_WDU_Mbstatet@@@6@@Z) referenced in function "char const * __cdecl boost::archive::test_filename(char const *,char *)" (?test_filename@archive@boost@@YAPEBDPEBDPEAD@Z)  
test_array.obj : error LNK2019: unresolved external symbol "void __cdecl boost::filesystem::detail::path_traits::convert(wchar_t const *,wchar_t const *,class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > &,class std::codecvt<wchar_t,char,struct _Mbstatet> const *)" (?convert@path_traits@detail@filesystem@boost@@YAXPEB_W0AEAV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@PEBV?$codecvt@_WDU_Mbstatet@@@6@@Z) referenced in function "char const * __cdecl boost::archive::test_filename(char const *,char *)" (?test_filename@archive@boost@@YAPEBDPEBDPEAD@Z)  
test_array.obj : error LNK2019: unresolved external symbol "class boost::filesystem::path __cdecl boost::filesystem::detail::unique_path(class boost::filesystem::path const &,class boost::system::error_code *)" (?unique_path@detail@filesystem@boost@@YA?AVpath@23@AEBV423@PEAVerror_code@system@3@@Z) referenced in function "char const * __cdecl boost::archive::test_filename(char const *,char *)" (?test_filename@archive@boost@@YAPEBDPEBDPEAD@Z)  
C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\serialization\test\test_array_text_archive.test\msvc-14.3\release\x86_64\threading-multi\test_array_text_archive.exe : fatal error LNK1120: 3 unresolved externals  
```  
  
These errors indicate that during compilation, the linker was unable to find the implementation of some functions in the boost::filesystem library. It is worth noting that the test failure of the serialization submodule occurs at the same time as the test failure of the filesystem submodule. I have already filed an issue https://github.com/boostorg/filesystem/issues/329 to report the test failure of filesystem. However, I do not know if there is any connection between the two modules, so I cannot be sure that the test failure of serialization is caused by the test failure of filesystem.  
  
refer test log for more detail . Thanks!  
[serialization-Test.log](https://github.com/user-attachments/files/17339431/serialization-Test.log)

---

## Comment 1

> Username: NEIL-smtg  
> Created at: 2024-10-14 07:03:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/322#issuecomment-2410206216  

Having the same issue here, it appears that it's missing Boost.Filesystem linkage. After I added the path to `boost_filesystem-vc143-mt-x64-1_87.lib` in the linker command, this issue appears to be resolved.

---

## Comment 2

> Username: vejbomar  
> Created at: 2025-02-03 13:16:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/322#issuecomment-2630981873  

This seems to be fixed in `develop` branch, most probably by https://github.com/boostorg/serialization/pull/317
