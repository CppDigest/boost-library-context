# #6 Most members of std::allocate are deprecated in C++17 [Merged]

> Username: DanielaE  
> Created at: 2018-01-01 10:15:18 UTC  
> Updated at: 2018-10-27 13:28:12 UTC  
> Merged at: 2018-10-27 13:28:12 UTC  
> Closed at: 2018-10-27 13:28:12 UTC  
> Url: https://github.com/boostorg/statechart/pull/6  

Replace them by their cousins from std::allocator_traits. In addition to that, std::allocator&lt;void&gt; (used as default argument to the 'Alloc' template parameter of some public types) is deprecated too, and needs some sort of emulation. The emulation type is a mere placeholder without ever being used. Without that, heaps of deprecation warnings will fall onto humble users when compiling with MSVC 15 in C++17 mode.  
  
This fixes #7

---
