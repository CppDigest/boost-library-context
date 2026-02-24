# #442 Add /manifest to clang-win.link [Merged]

> Username: pdimov  
> Created at: 2019-05-24 23:30:34 UTC  
> Updated at: 2021-10-02 21:14:02 UTC  
> Merged at: 2019-06-03 19:51:23 UTC  
> Closed at: 2019-06-03 19:51:23 UTC  
> Url: https://github.com/boostorg/build/pull/442  

Executables that contain "update" in their names, such as for instance `mp_map_update.exe` and `mp_map_update_q.exe` from the Mp11 test suite, automatically request elevation (at least on Windows 7.)  
  
This doesn't happen for the msvc toolset, because it passes `/manifest` to the linker, which generates a manifest file, which then gets embedded into the executable and suppresses the installer autodetection.  
  
The clang-win toolset, however, didn't pass `/manifest` to the linker, so no manifest was produced, and even though the `msvc.manifest` rule was invoked, it did nothing.

---
