# #78 Make `basic_static_string` constructible and assignable from a C-style string in a constexpr context [Merged]

> Username: gennaroprota  
> Created at: 2025-12-15 15:30:19 UTC  
> Updated at: 2025-12-16 09:30:44 UTC  
> Merged at: 2025-12-16 09:30:34 UTC  
> Closed at: 2025-12-16 09:30:34 UTC  
> Url: https://github.com/boostorg/static_string/pull/78  

Constructing or assigning from a C-style string in a constexpr context triggers a libstdc++ issue which was fixed in GCC 12.4 and 13.3: <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=113200>. We add corresponding tests, and then a workaround for the issue.

---
