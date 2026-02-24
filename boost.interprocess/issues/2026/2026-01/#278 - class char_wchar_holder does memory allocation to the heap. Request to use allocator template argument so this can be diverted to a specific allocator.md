# #278 - class char_wchar_holder does memory allocation to the heap. Request to use allocator template argument so this can be diverted to a specific allocator. [Open]

> Username: cminnoy  
> Created at: 2026-01-30 16:37:14 UTC  
> Updated at: 2026-01-30 16:37:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/278  

Situation:  
* Recent versions use the class char_wchar_holder to hold the filename.  
   Used in file_mapping.hpp, shared_memory_object.hpp and windows_shared_memory.hpp  
Issue:  
* On specific targets doing heap allocation ain't allowed, often for real-time requirements.  
Requested solution:  
* Adapt char_wchar_holder to use an alternative allocator as template argument.  
* Adapt the classes using char_wchar_holder to pass along the requested allocator.  
* Stick to STL allocator interfacing to maximize compatibility.
