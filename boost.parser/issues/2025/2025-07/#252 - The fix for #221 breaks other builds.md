# #252 - The fix for #221 breaks other builds [Closed]

> Username: tzlaine  
> Created at: 2025-07-27 01:58:42 UTC  
> Updated at: 2025-07-27 02:01:08 UTC  
> Closed at: 2025-07-27 02:01:08 UTC  
> Url: https://github.com/boostorg/parser/issues/252  

The fix for #221 was to add `template` before naming a dependent name in the implementation of `project_view::iterator`.  This, however, breaks older C++17 builds (but not newer ones; sigh).
