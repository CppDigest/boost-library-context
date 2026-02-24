# #355 native_environment_impl::get method bugfix for Windows wchar_t [Merged]

> Username: mknaleczb  
> Created at: 2024-02-11 22:32:51 UTC  
> Updated at: 2024-03-31 22:36:48 UTC  
> Merged at: 2024-03-31 22:36:48 UTC  
> Closed at: 2024-03-31 22:36:48 UTC  
> Url: https://github.com/boostorg/process/pull/355  

Issue description: for Windows using MSVC when get method is invoked using native_environment_impl<Char=wchar_t> object compilation error C2440 (conversion from const char[] to std::wstring) is invoked  
  
Reproduction: https://godbolt.org/z/4rMbzPKE5  
Proposed solution: change of return value to empty std::basic_string<Char> object

---
