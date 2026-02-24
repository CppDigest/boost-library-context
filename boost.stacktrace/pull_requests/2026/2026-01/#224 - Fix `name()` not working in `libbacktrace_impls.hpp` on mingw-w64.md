# #224 Fix `name()` not working in `libbacktrace_impls.hpp` on mingw-w64 [Open]

> Username: prbegd  
> Created at: 2026-01-30 03:35:52 UTC  
> Updated at: 2026-01-30 03:35:52 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/224  

Fixes #212  
  
## Changes  
  
In `libbacktrace_impls.hpp`:   
- `libbacktrace_full_callback()` will now return `1` when there is valid data. So `or` case will only call `backtrace_syminfo` when the data returned from `backtrace_pcinfo` is invalid.  
- Rebase the address (Virtual Address) before passing it to `backtrace_syminfo` in `name_impl`. Add `read_static_image_base` to parse the executable and read the Image Base stored in it.

---
