# #109 - MSVC C++ 20 With Diagnostic Services [Open]

> Username: ajorians  
> Created at: 2023-10-12 17:32:46 UTC  
> Updated at: 2023-10-12 18:21:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/109  

The following information is odd anyway and as you'll see is easy to resolve on our side.  Meaning I wasn't thinking any actions need to be done at all; but figured at least let you know about it.  
  
First consider Microsoft has the ability to detect memory leaks with some [Diagnostic Services](https://learn.microsoft.com/en-us/cpp/mfc/reference/diagnostic-services?view=msvc-170) via redefining the `new` operator:  
![image](https://github.com/boostorg/smart_ptr/assets/3475163/761a7cba-09e8-458e-9307-4f288d6cd8a0)  
  
With C++17 this isn't a problem as you can see with this online link: https://godbolt.org/z/nx35hhY9r  
  
(note: Compiler Explorer doesn't install with MFC/ATL but those defines should give similar effect).  
  
It builds.  But if you change the setting to build as C++20 (or later) it fails to compile; here is an online link: https://godbolt.org/z/M3ba6r48h  
![image](https://github.com/boostorg/smart_ptr/assets/3475163/dd13641c-5ecc-4c66-b8bd-e0f99692a6cd)  
  
I, myself, am using `boost` 1.81.0 where I am noticing this issue.  
  
Hope that helps!

---

## Comment 1

> Username: pdimov  
> Created at: 2023-10-12 18:21:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/109#issuecomment-1760141785  

The reason for the difference is that MSVC is using the old compiler frontend (`/permissive`) in C++17 mode and the new one in C++20 mode (`/permissive-`).  
  
In "permissive" mode, syntax errors in templates that aren't used (such as the above `shared_count` constructor that takes an allocator) do not cause compilation to fail, but in "permissive-" mode, they do.  
  
Since the code is already passing arguments to `new`, the macro that passes file/line causes a syntax error.
