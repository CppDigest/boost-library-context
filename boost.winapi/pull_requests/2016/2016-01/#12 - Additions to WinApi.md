# #12 Additions to WinApi [Closed]

> Username: klemens-morgenstern  
> Created at: 2016-01-24 13:06:08 UTC  
> Updated at: 2016-01-25 07:36:35 UTC  
> Closed at: 2016-01-25 01:21:36 UTC  
> Url: https://github.com/boostorg/winapi/pull/12  

I removed all direct inclusions of windows.h in boost/process and replaced them in the boost.winapi style. I hope this library would be the right place for those additions, so boost.process can use them.

---

## Comment 1

> Username: Lastique  
> Created_at: 2016-01-25 01:21:36 UTC  
> Url: https://github.com/boostorg/winapi/pull/12#issuecomment-174365758  

I had a quick look and unfortunately I cannot accept this PR in its current form. Here are some comments:  
- All new files must have a proper copyright and license note.  
- No `__declspec` and raw types in the declarations. Use the corresponding macros and typedefs.  
- No function declarations that are actually macros in the Windows SDK. This mostly concerns functions that accept strings. For each such function there should be two declarations (`*A` and `*W`), the narrow one should be conditioned on `BOOST_NO_ANSI_APIS`. Where appropriate, there can be a lower-case overload that calls the correct function depending on the argument types.  
- When `windows.h` is not included all functions must be declared in the global namespace. When no type punning is required, the functions can be simply imported into `boost::detail::winapi`. Otherwise, there should be an inline wrapper function in `boost::detail::winapi` that performs type conversions and calls the real function in the global namespace.  
- All constants should be declared with the same case as the original constants in Windows SDK, with an underline appended. E.g. `const DWORD_ DEBUG_PROCESS_ = DEBUG_PROCESS;`. The lower-case versions are optional and are provided in addition to the upper-case ones, where possible (e.g. when they don't clash with something else).  
- No `extern "C"` in `boost::detail::winapi`.  
- All structures must be re-defined in `boost::detail::winapi` in an ABI-compatible way to the originals in Windows SDK. When `windows.h` is not included, the original structs must be forward-declared in the global namespace. When a struct is involved in a function interface, the inline wrapper in `boost::detail::winapi` for the function must be present.  
- No dependency on `UNICODE`, please. All structs must have stable implementation regardless of the macros.  
- No `TCHAR`, please. It should not exist.  
- There's no `DWOD_` type (see show_windows.hpp). Run the tests first, please.  
- Actually, I'm not sure I like that show_windows.hpp adds constants that seem to be related to the windowing system to Boost.WinAPI. I'm not sure this is the right place for them.  
  
All in all, have a look at the other headers in Boost.WinAPI and try to follow the existing practice.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2016-01-25 07:36:35 UTC  
> Url: https://github.com/boostorg/winapi/pull/12#issuecomment-174427354  

Thank you for the comments. I'll incorporate your comments and hopefully it'll be a better PR next time.

---
