# #148 - Wrong BOOST_PFR_CORE_NAME_PARSING with "clang for MSVC" [Closed]

> Username: bansan85  
> Created at: 2023-11-28 12:55:39 UTC  
> Updated at: 2023-12-12 07:46:37 UTC  
> Closed at: 2023-12-12 07:46:20 UTC  
> Url: https://github.com/boostorg/pfr/issues/148  

Under Windows with Visual Studio Installer, install "clang compiler for Windows"  
  
With CMake, generate a project with toolset "ClangCL" : `-G"Visual Studio 2022" -T ClangCL`.  
  
Build.  
  
In this case, `_MSC_VER` is defined but I need to use the `BOOST_PFR_CORE_NAME_PARSING` from `__clang__`.  
  
https://stackoverflow.com/questions/67599384/distinguish-between-clang-cl-and-msvc-cl  
  
Solution is to check `__clang__` first :  
  
```  
#ifndef BOOST_PFR_CORE_NAME_PARSING  
#   if defined(__clang__)  
#       define BOOST_PFR_CORE_NAME_PARSING (sizeof("auto boost::pfr::detail::name_of_field_impl() [MsvcWorkaround = ") - 1, sizeof("}]") - 1, backward("."))  
#   elif defined(_MSC_VER)  
#       define BOOST_PFR_CORE_NAME_PARSING (sizeof("auto __cdecl boost::pfr::detail::name_of_field_impl<") - 1, sizeof(">(void) noexcept") - 1, backward("->"))  
#   elif defined(__GNUC__)  
#       define BOOST_PFR_CORE_NAME_PARSING (sizeof("consteval auto boost::pfr::detail::name_of_field_impl() [with MsvcWorkaround = ") - 1, sizeof(")]") - 1, backward("::"))  
#   else  
// Default parser for other platforms... Just skip nothing!  
#       define BOOST_PFR_CORE_NAME_PARSING (0, 0, "")  
#   endif  
#endif  
```

---

## Comment 1

> Username: denzor200  
> Created at: 2023-11-28 21:55:53 UTC  
> Url: https://github.com/boostorg/pfr/issues/148#issuecomment-1830823045  

Here is the fix: https://github.com/boostorg/pfr/pull/149

---

## Comment 2

> Username: bansan85  
> Created at: 2023-12-12 07:46:20 UTC  
> Updated at: 2023-12-12 07:46:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/148#issuecomment-1851459387  

Close since fix works. Thanks.
