# #200 Use Relative Virtual Addresses to allow their decoding without knowing the base address [Merged]

> Username: McCzarny  
> Created at: 2025-01-01 11:06:26 UTC  
> Updated at: 2025-01-07 21:00:09 UTC  
> Merged at: 2025-01-07 20:59:16 UTC  
> Closed at: 2025-01-07 20:59:16 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200  

Instead of printing absolute addresses use relative ones so they can be used later to decode the stack trace.  
  
## Motivation  
  
It’s quite common to release apps without debug symbols while keeping them internally. Currently the lib prints absolute addresses which require the base address to decode them using symbols. With this change, the base address won’t be needed as values are relative to the beginning of the loaded module/binary.  
  
The implementation for unix is **straightforward** as the existing code is used. For Windows, I’ve implemented similar logic using Windows API.  
  
## Manual testing of Windows implementation  
  
Here are 2 runs of `trivial_windbg_lib.exe` with .pdb and without .pdb file:  
  
```jsx  
>./trivial_windbg_lib.exe  
 0# boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> >::init at C:\Users\czarneckim\repositories\boost\boost\stacktrace\stacktrace.hpp:110  
 1# main at C:\Users\czarneckim\repositories\boost\libs\stacktrace\test\test_trivial.cpp:14  
 2# __scrt_common_main_seh at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:288  
 3# BaseThreadInitThunk in C:\WINDOWS\System32\KERNEL32.DLL  
 4# RtlUserThreadStart in C:\WINDOWS\SYSTEM32\ntdll.dll  
  
> mv ./trivial_windbg_lib.pdb{,_}   
> ./trivial_windbg_lib.exe  
 0# 0x0000000000001DE3 in C:\Users\czarneckim\repositories\boost\bin.v2\libs\stacktrace\test\trivial_windbg_lib.test\msvc-14.3\release\x86_64\asynch-exceptions-on\cxxstd-latest-iso\debug-symbols-on\threading-multi\trivial_windbg_lib.exe  
 1# 0x0000000000001FEE in C:\Users\czarneckim\repositories\boost\bin.v2\libs\stacktrace\test\trivial_windbg_lib.test\msvc-14.3\release\x86_64\asynch-exceptions-on\cxxstd-latest-iso\debug-symbols-on\threading-multi\trivial_windbg_lib.exe  
 2# 0x000000000000246C in C:\Users\czarneckim\repositories\boost\bin.v2\libs\stacktrace\test\trivial_windbg_lib.test\msvc-14.3\release\x86_64\asynch-exceptions-on\cxxstd-latest-iso\debug-symbols-on\threading-multi\trivial_windbg_lib.exe  
 3# BaseThreadInitThunk in C:\WINDOWS\System32\KERNEL32.DLL  
 4# RtlUserThreadStart in C:\WINDOWS\SYSTEM32\ntdll.dll  
  
> mv ./trivial_windbg_lib.pdb{_,}  
> winaddr2line.exe -f -e trivial_windbg_lib.pdb 0x0000000000001DE3  
boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> >::init  
C:\Users\czarneckim\repositories\boost\boost\stacktrace\stacktrace.hpp:110  
```  
  
I’ve also decoded the returned address using x64dbg running `trivial_windbg_lib.exe+0x0000000000001DE3`:  
  
![image](https://github.com/user-attachments/assets/a36d3306-488d-4610-9f04-7888b98c2b75)  
  
## Control  
  
As it was suggested in #180, the new logic is enabled by default, but can be disabled with `BOOST_STACKTRACE_DISABLE_OFFSET_ADDR_BASE` (With non header-only mode, it requires rebuilding the lib)  
  
## Further work  
  
If the implementation would be accepted, I’m happy to do the rest of work, like changes in documentation or test cases. It’s my first contribution to this repo, so I would be very grateful for some guidance what is required.

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2025-01-01 12:18:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/stacktrace/pull/200#pullrequestreview-2526845060  

Looks good! Requested a few changes. Do you have ideas, how to autotest the offsets?

📁 include/boost/stacktrace/detail/addr_base_msvc.hpp

```diff
  52 |+ 
  53 |+                 // Get the module name
  54 |+                 if (GetModuleBaseName(process_handle, modules[i], module_name, sizeof(module_name) / sizeof(TCHAR))
```

> Username: apolukhin  
> Created_at: 2025-01-01 12:04:11 UTC  
> Updated_at: 2025-01-01 12:18:22 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1900384253  

What is the point of getting module name if it is not used afterwards?

---

📁 include/boost/stacktrace/detail/addr_base_msvc.hpp

```diff
  30 |+         // The stack buffer should be large enough for most processes.
  31 |+         HMODULE modules_stack[1024];
  32 |+         HMODULE* modules_allocated = nullptr;
```

> Username: apolukhin  
> Created_at: 2025-01-01 12:07:22 UTC  
> Updated_at: 2025-01-01 12:18:22 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1900384667  

It is better to use std::unique_ptr<HMODULE[]> here.

---

📁 include/boost/stacktrace/detail/addr_base_msvc.hpp

```diff
  47 |+ 
  48 |+         if (enum_process_result) {
  49 |+             for (int i = 0; i < (needed_bytes / sizeof(HMODULE)); ++i) {
```

> Username: apolukhin  
> Created_at: 2025-01-01 12:11:49 UTC  
> Updated_at: 2025-01-01 12:18:22 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1900385457  

std::size_t

---

📁 include/boost/stacktrace/detail/addr_base_msvc.hpp

```diff
  26 |+ 
  27 |+ namespace boost { namespace stacktrace { namespace detail {
  28 |+   inline  uintptr_t get_own_proc_addr_base(const void* addr) {
```

> Username: apolukhin  
> Created_at: 2025-01-01 12:12:09 UTC  
> Updated_at: 2025-01-01 12:18:22 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1900385510  

std::uintptr_t

---

📁 include/boost/stacktrace/detail/addr_base_msvc.hpp

```diff
  56 |+                     && module_info.lpBaseOfDll <= addr && addr < LPBYTE(module_info.lpBaseOfDll) + module_info.SizeOfImage) {
  57 |+                     // Module contains the address
  58 |+                 addr_base = reinterpret_cast<uintptr_t>(module_info.lpBaseOfDll);
```

> Username: apolukhin  
> Created_at: 2025-01-01 12:12:19 UTC  
> Updated_at: 2025-01-01 12:18:22 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1900385548  

std::uintptr_t

---

📁 include/boost/stacktrace/detail/addr_base_msvc.hpp

```diff
  12 |+ #   pragma once
  13 |+ #endif
  14 |+ 
```

> Username: apolukhin  
> Created_at: 2025-01-01 12:12:50 UTC  
> Updated_at: 2025-01-01 12:18:22 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1900385656  

`#include <cstdint>`

---

📁 include/boost/stacktrace/detail/addr_base_msvc.hpp

```diff
  42 |+         if (!enum_process_result && GetLastError() == ERROR_INSUFFICIENT_BUFFER) {
  43 |+             modules_allocated = new HMODULE[needed_bytes / sizeof(HMODULE)];
  44 |+             enum_process_result = EnumProcessModules(process_handle, modules, sizeof(modules), &needed_bytes);
```

> Username: apolukhin  
> Created_at: 2025-01-01 12:16:10 UTC  
> Updated_at: 2025-01-01 12:18:22 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1900386097  

Looks like `  modules = modules_allocated; should go before the second EnumProcessModules call, and not `sizeof(modules) ` should be passed


---

## Comment 2

> Username: coveralls  
> Created_at: 2025-01-01 12:20:30 UTC  
> Updated_at: 2025-01-07 18:11:29 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#issuecomment-2566985731  

[![Coverage Status](https://coveralls.io/builds/71622116/badge)](https://coveralls.io/builds/71622116)  
  
coverage: 86.371% (+0.02%) from 86.35%  
when pulling **bf9b566d2d68f1aae7b69f0a081e461b0abdfcac on McCzarny:develop**  
into **5ec45912dafb4e8b4115644a3a683ed5cfdfdc9d on boostorg:develop**.

---

## Comment 3

> Username: McCzarny  
> Created_at: 2025-01-02 10:49:59 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#issuecomment-2567583249  

> Looks good! Requested a few changes. Do you have ideas, how to autotest the offsets?  
  
The simplest solution might be to expect addresses in some range (maybe it's possible to read it from the file itself - ELF?), but this won't be very strict and will miss small differences.  
  
A more complex way would be to test executables that have debug symbols in separate files (.pdb and unstripped/stripped with --only-keep-debug). For Windows, [Dbghelp](https://learn.microsoft.com/en-us/windows/win32/debug/debug-help-library)  is sufficient to convert RVA to a symbol. The library is available on the `windows-latest` image with `visualstudio2022buildtools ` installed, not sure about older versions. For Linux, I don't know if it's possible to do the same with a minimal setup.  
  
Another thing that comes to mind is that in an `ASLR` env we can run an executable multiple times and expect the same result.  
  
How complex could such a test be? I can imagine it being run with a lot of setups, so it should use some platform specific libs or tools.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2025-01-02 11:23:57 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#issuecomment-2567626376  

> How complex could such a test be?  
  
Just make a smoke test. Check that the address is less than the size of a binary you are working with. Path to the binary could be obtained from argv[0], and the size could be retrieved via   
```  
std::ifstream file(argv[0], std::ios::binary | std::ios::ate);  
return file.tellg();  
```

---

## Review 5 [Commented]

> Username: McCzarny  
> Created_at: 2025-01-06 16:17:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/200#pullrequestreview-2532485449  

📁 test/test.cpp

```diff
 294 |+     const auto frame = to_string(boost::stacktrace::stacktrace(0, 1).as_vector().front());
 295 |+ 
 296 |+     // Skip the test if the frame does not contain an address
```

> Username: McCzarny  
> Created_at: 2025-01-06 16:15:34 UTC  
> Updated_at: 2025-01-06 16:17:14 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1904350772  

I was trying to use `BOOST_STACKTRACE_TEST_SHOULD_OUTPUT_READABLE_NAMES` but there were configurations were it was set to 0, but the stacktrace had a function name included. Let me know if you're fine with this part.

---

📁 test/test.cpp

```diff
 266 | 
 266 |- int main() {
 267 |+ std::size_t get_file_size(const char* file_name) {
```

> Username: McCzarny  
> Created_at: 2025-01-06 16:16:51 UTC  
> Updated_at: 2025-01-06 16:17:14 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1904352373  

Should these helper functions be here or you prefer to move them to `test_imp`?

> Username: apolukhin  
> Created_at: 2025-01-07 17:34:15 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#discussion_r1905827972  

It is fine to remain here


---

## Comment 6

> Username: apolukhin  
> Created_at: 2025-01-07 21:00:07 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/200#issuecomment-2576210517  

Many thanks for the PR!

---
