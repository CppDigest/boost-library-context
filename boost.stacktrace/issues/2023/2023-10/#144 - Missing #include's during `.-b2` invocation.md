# #144 - Missing #include's during `./b2` invocation [Closed]

> Username: cmazakas  
> Created at: 2023-10-14 22:09:20 UTC  
> Updated at: 2023-10-21 08:57:17 UTC  
> Closed at: 2023-10-21 08:57:03 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/144  

When invoking `./b2` in the boost project root, I'm getting compilation failures such as these:  
```  
./boost/stacktrace/detail/addr2line_impls.hpp:162:9: error: ‘addr_base’ was not declared in this scope  
  162 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
      |         ^~~~~~~~~  
./boost/stacktrace/detail/addr2line_impls.hpp:162:48: error: ‘get_own_proc_addr_base’ is not a member of ‘boost::stacktrace::detail’  
  162 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
      |                                                ^~~~~~~~~~~~~~~~~~~~~~  
./boost/stacktrace/detail/addr2line_impls.hpp:164:67: error: ‘uintptr_t’ does not name a type  
  164 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
      |                                                                   ^~~~~~~~~  
./boost/stacktrace/detail/addr2line_impls.hpp:164:67: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
./boost/stacktrace/detail/addr2line_impls.hpp:164:86: error: ‘addr_base’ was not declared in this scope  
  164 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
      |                                                                                      ^~~~~~~~~  
./boost/stacktrace/detail/addr2line_impls.hpp: In function ‘std::string boost::stacktrace::detail::name(const void*, bool)’:  
./boost/stacktrace/detail/addr2line_impls.hpp:207:5: error: ‘uintptr_t’ was not declared in this scope  
  207 |     uintptr_t addr_base = 0;  
      |     ^~~~~~~~~  
  
```  
  
I'm on Linux (Ubuntu 23.10) using gcc-13.

---

## Comment 1

> Username: apolukhin  
> Created at: 2023-10-21 08:57:03 UTC  
> Updated at: 2023-10-21 08:57:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/144#issuecomment-1773723773  

Fixed in #143   
  
Thanks for the bug report!
