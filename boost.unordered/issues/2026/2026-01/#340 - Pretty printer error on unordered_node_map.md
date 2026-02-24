# #340 - Pretty printer error on unordered_node_map [Closed]

> Username: indiosmo  
> Created at: 2026-01-22 02:09:58 UTC  
> Updated at: 2026-01-27 19:47:26 UTC  
> Closed at: 2026-01-27 19:47:26 UTC  
> Url: https://github.com/boostorg/unordered/issues/340  

Boost: 1.90.0  
GCC: 15.2  
  
Trying to print a boost::unordered_node_map give an error:  
  
```cpp  
#include <boost/unordered/unordered_node_map.hpp>  
#include <string>  
  
int main()  
{  
  boost::unordered_node_map<int, std::string> orders;  
  orders[1] = "first";  
  orders[2] = "second";  
  
  return 0;  
}  
```  
  
```bash  
$ gdb _build/debug/sandbox/sandbox  
GNU gdb (Ubuntu 15.0.50.20240403-0ubuntu1) 15.0.50.20240403-git  
Copyright (C) 2024 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  
Type "show copying" and "show warranty" for details.  
This GDB was configured as "x86_64-linux-gnu".  
Type "show configuration" for configuration details.  
For bug reporting instructions, please see:  
<https://www.gnu.org/software/gdb/bugs/>.  
Find the GDB manual and other documentation resources online at:  
    <http://www.gnu.org/software/gdb/documentation/>.  
  
For help, type "help".  
Type "apropos word" to search for commands related to "word"...  
Reading symbols from _build/debug/sandbox/sandbox...  
(gdb) b main.cpp:10  
Breakpoint 1 at 0x206c1d: file /home/msi/abacus_workspace/abacus/sandbox/main.cpp, line 10.  
(gdb) run  
Starting program: /home/msi/abacus_workspace/abacus/_build/debug/sandbox/sandbox  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
  
Breakpoint 1, main () at /home/msi/abacus_workspace/abacus/sandbox/main.cpp:10  
10        return 0;  
(gdb) p orders  
$1 = boost::unordered_node_map with 2 elementsPython Exception <class 'gdb.error'>: There is no member or method named first.  
```

---

## Comment 1

> Username: k3DW  
> Created at: 2026-01-22 04:18:06 UTC  
> Url: https://github.com/boostorg/unordered/issues/340#issuecomment-3782425705  

I'm not able to reproduce the issue you're seeing, also using Boost 1.90.0 with GCC 15.2. Can you share the flags you used to compile with GCC?  
  
I did this:  
```  
$ g++ --version  
g++ (Ubuntu 15.2.0-4ubuntu4) 15.2.0  
Copyright (C) 2025 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
$ g++ ./main.cpp -Ipath/to/boost -g  
$ gdb  
GNU gdb (Ubuntu 16.3-1ubuntu2) 16.3  
Copyright (C) 2024 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  
Type "show copying" and "show warranty" for details.  
This GDB was configured as "x86_64-linux-gnu".  
Type "show configuration" for configuration details.  
For bug reporting instructions, please see:  
<https://www.gnu.org/software/gdb/bugs/>.  
Find the GDB manual and other documentation resources online at:  
    <http://www.gnu.org/software/gdb/documentation/>.  
  
For help, type "help".  
Type "apropos word" to search for commands related to "word".  
(gdb) file a.out  
Reading symbols from a.out...  
(gdb) break main.cpp:10  
Breakpoint 1 at 0x2d59: file ./main.cpp, line 10.  
(gdb) run  
Starting program: ./a.out   
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
  
Breakpoint 1, main () at ./main.cpp:10  
10        return 0;  
(gdb) print orders  
$1 = boost::unordered_node_map with 2 elements = {[2] = "second", [1] = "first"}  
```

---

## Comment 2

> Username: indiosmo  
> Created at: 2026-01-22 10:57:35 UTC  
> Updated at: 2026-01-22 11:24:32 UTC  
> Url: https://github.com/boostorg/unordered/issues/340#issuecomment-3783779614  

Hi, flags below.  
I also updated to gdb 17.1 and still fails.  
  
Python version: 3.12.3  
  
Relevant flags and defines:  
```  
-D_GLIBCXX_ASSERTIONS  
-g  
-std=c++26  
-ggdb  
-fdiagnostics-color=always  
-fno-strict-aliasing  
-fuse-ld=mold  
-Wl,--no-undefined  
-fno-omit-frame-pointer  
-fconcepts-diagnostics-depth=2  
-march=native  
```  
  
Full build command:  
```json  
  {  
    "directory": "/home/msi/abacus_workspace/abacus/_build/debug",  
    "command": "/home/msi/abacus_workspace/gcc-15.2.0/bin/g++ -DBOOST_ASIO_CONCURRENCY_HINT_DEFAULT=1 -DBOOST_LEAF_CFG_GNUC_STMTEXPR=0 -DJSON_DIAGNOSTICS=1 -DMIL_ERROR_ALWAYS_GENERATE_STACKTRACE=1 -DMIL_FUNCTION_ENABLE_NON_EMPTY_ASSERTS=ON -DMIL_LOG_ACTIVE_LEVEL=MIL_LOG_LEVEL_TRACE -DSPDLOG_FMT_EXTERNAL -D_GLIBCXX_ASSERTIONS -I/home/msi/abacus_workspace/abacus/src/mil -isystem /hom  e/msi/abacus_workspace/boost_1_90_0/include -isystem /home/msi/abacus_workspace/abacus/vendor/magic_enum/include -isystem /home/msi/abacus_workspace/abacus/vendor/readerwriterqueue/include -isystem /home/msi/abacus_workspace/abacus/vendor/decimal_for_cpp/include -isystem /home/msi/abacus/build/src/nlohmann_json_v3.12.0/include -isystem /home/msi/abacus/build/src/NamedType_v1.1.1/  include -isystem /home/msi/abacus_workspace/abacus/vendor/plf/include -isystem /home/msi/abacus_workspace/abacus/vendor/scope_guard/include -isystem /home/msi/abacus/build/install/Debug/fmt_12.1.0/include -isystem /home/msi/abacus/build/install/Debug/spdlog_v1.16.0/include -isystem /home/msi/abacus/build/install/Debug/oneTBB_v2022.3.0/include -isystem /home/msi/abacus/build/insta  ll/Debug/cpptrace_v1.0.4/include -isystem /home/msi/abacus/build/src/tl-optional_v1.1.0/include -g -std=c++26 -Wall -Wextra -Wconversion -Wnarrowing -Wdouble-promotion -ggdb -fdiagnostics-color=always -fno-strict-aliasing -fuse-ld=mold -Wl,--no-undefined -fno-omit-frame-pointer -fconcepts-diagnostics-depth=2 -march=native -Wpedantic -Werror -pedantic-errors -Wnull-dereference -Wd  angling-reference -Wduplicated-cond -Wduplicated-branches -Wunused-function -Wlogical-op -Wrestrict -Wcast-align=strict -Wnrvo -Wuninitialized -Walloca -Wcast-qual -Wno-error=deprecated-declarations -Wcomma-subscript -Wctor-dtor-privacy -Wdeprecated-copy-dtor -Wenum-conversion -Wformat-overflow=2 -Wformat-signedness -Wformat=2 -Wmultichar -Wnon-virtual-dtor -Wpointer-arith -Wrang  e-loop-construct -Wstrict-null-sentinel -Wsuggest-attribute=format -Wsuggest-attribute=malloc -Wvla -Wvolatile -Wwrite-strings -Wundef -Wimplicit-fallthrough -Wextra-semi -o sandbox/CMakeFiles/sandbox.dir/main.cpp.o -c /home/msi/abacus_workspace/abacus/sandbox/main.cpp",  
    "file": "/home/msi/abacus_workspace/abacus/sandbox/main.cpp",  
    "output": "/home/msi/abacus_workspace/abacus/_build/debug/sandbox/CMakeFiles/sandbox.dir/main.cpp.o"  
  },  
```   
  
```bash  
$ gdb ./_build/debug/sandbox/sandbox  
GNU gdb (GDB) 17.1  
Copyright (C) 2025 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  
Type "show copying" and "show warranty" for details.  
This GDB was configured as "x86_64-pc-linux-gnu".  
Type "show configuration" for configuration details.  
For bug reporting instructions, please see:  
<https://www.gnu.org/software/gdb/bugs/>.  
Find the GDB manual and other documentation resources online at:  
    <http://www.gnu.org/software/gdb/documentation/>.  
  
For help, type "help".  
Type "apropos word" to search for commands related to "word"...  
Reading symbols from ./_build/debug/sandbox/sandbox...  
(gdb) b main.cpp:10  
Breakpoint 1 at 0x206c1d: file /home/msi/abacus_workspace/abacus/sandbox/main.cpp, line 10.  
(gdb) run  
Starting program: /home/msi/abacus_workspace/abacus/_build/debug/sandbox/sandbox  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
  
Breakpoint 1, main () at /home/msi/abacus_workspace/abacus/sandbox/main.cpp:10  
10        return 0;  
(gdb) p orders  
$1 = boost::unordered_node_map with 2 elementsPython Exception <class 'gdb.error'>: There is no member or method named first.  
```

---

## Comment 3

> Username: k3DW  
> Created at: 2026-01-23 04:15:48 UTC  
> Url: https://github.com/boostorg/unordered/issues/340#issuecomment-3788115755  

There may be something wrong with your stdlib pretty-printers. If you create a `std::pair<int, std::string>`, does this print successfully under these same compile conditions?

---

## Comment 4

> Username: indiosmo  
> Created at: 2026-01-23 22:01:59 UTC  
> Url: https://github.com/boostorg/unordered/issues/340#issuecomment-3792721768  

Yes, std::pair works ` std::pair<int, std::string> test_pair{42, "hello"};`  
  
```  
Breakpoint 1, main () at /home/msi/abacus_workspace/abacus/sandbox/main.cpp:14  
14        return 0;  
(gdb) p test_pair  
$1 = {  
  first = 42,  
  second = "hello"  
}  
(gdb) p orders  
$2 = boost::unordered_node_map with 2 elementsPython Exception <class 'gdb.error'>: There is no member or method named first.  
  
(gdb)  
```

---

## Comment 5

> Username: indiosmo  
> Created at: 2026-01-23 22:21:29 UTC  
> Updated at: 2026-01-23 22:23:19 UTC  
> Url: https://github.com/boostorg/unordered/issues/340#issuecomment-3792818480  

I have zero familiarity with writing pretty printers so I can't judge the quality of the patch, but I took the liberty of asking the AI to debug it, and it came up with a fix that seems to work.  
  
I'm attaching the patch and it's explanation.  
Sorry if this is not appropriate, but maybe it can at least point you in some direction if it's not a proper fix.  
  
  
```  
$ gdb ./_build/debug/sandbox/sandbox  
GNU gdb (Ubuntu 15.0.50.20240403-0ubuntu1) 15.0.50.20240403-git  
(gdb) source /home/msi/abacus_workspace/boost_1_90_0/libs/unordered/extra/boost_unordered_printers.py  
(gdb) b main.cpp:14  
Breakpoint 1 at 0x207315: file /home/msi/abacus_workspace/abacus/sandbox/main.cpp, line 14.  
(gdb) ru  
Starting program: /home/msi/abacus_workspace/abacus/_build/debug/sandbox/sandbox  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
  
Breakpoint 1, main () at /home/msi/abacus_workspace/abacus/sandbox/main.cpp:14  
14        return 0;  
(gdb) p orders  
$1 = boost::unordered_node_map with 2 elements = {  
  [2] = "second",  
  [1] = "first"  
}  
(gdb) quit  
```  
  
```diff  
diff --git a/libs/unordered/extra/boost_unordered_printers.py b/libs/unordered/extra/boost_unordered_printers.py  
--- a/libs/unordered/extra/boost_unordered_printers.py  
+++ b/libs/unordered/extra/boost_unordered_printers.py  
@@ -15,11 +15,15 @@ class BoostUnorderedHelpers:  
         else:  
             return n  
  
     def maybe_unwrap_foa_element(e):  
-        if f"{e.type.strip_typedefs()}".startswith("boost::unordered::detail::foa::element_type<"):  
-            return e["p"]  
-        else:  
-            return e  
+        underlying_type = e.type.strip_typedefs()  
+        # Handle pointer to element_type (from iterating elements array)  
+        if underlying_type.code == gdb.TYPE_CODE_PTR:  
+            target_type = f"{underlying_type.target().strip_typedefs()}"  
+            if target_type.startswith("boost::unordered::detail::foa::element_type<"):  
+                return e.dereference()["p"]  
+        # Handle element_type directly  
+        elif f"{underlying_type}".startswith("boost::unordered::detail::foa::element_type<"):  
+            return e["p"]  
+        return e  
  
     def maybe_unwrap_reference(value):  
         if value.type.code == gdb.TYPE_CODE_REF:  
```  
  
  
  
# GDB Pretty Printer Bug Fix for unordered_node_map  
  
## Summary  
  
The GDB pretty printer for `boost::unordered_node_map` (and other node-based FOA containers) fails with:  
  
```  
Python Exception <class 'gdb.error'>: There is no member or method named first.  
```  
  
## Root Cause  
  
In `BoostUnorderedFoaPrinter.children()`, the variable `p_` (derived from `self.elements`) is passed to `maybe_unwrap_foa_element()`. For node containers, `p_` points to an `element_type<pair<K,V>, VoidPtr>` struct which contains a member `p` that points to the actual key-value pair.  
  
The original `maybe_unwrap_foa_element` function checks if the type starts with `"boost::unordered::detail::foa::element_type<"`:  
  
```python  
def maybe_unwrap_foa_element(e):  
    if f"{e.type.strip_typedefs()}".startswith("boost::unordered::detail::foa::element_type<"):  
        return e["p"]  
    else:  
        return e  
```  
  
However, `e` is a **pointer** to `element_type`, not the struct itself. The type is:  
  
```  
boost::unordered::detail::foa::table_arrays<...>::value_type_pointer  
```  
  
This is a typedef (GDB type code 23 = `TYPE_CODE_TYPEDEF`) for a pointer to `element_type`. The original code has two issues:  
  
1. It checks `e.type.strip_typedefs()` which strips the typedef but still results in a pointer type string like `element_type<...> *`, not matching the expected prefix properly in all cases.  
  
2. More importantly, even if the string check passed, `e["p"]` would fail because you cannot access struct members directly on a pointer value in GDB Python - you must dereference first.  
  
Because the check fails, `e` is returned unchanged, and the subsequent code dereferences it to get the `element_type` struct (not the pair), then tries to access `value["first"]` which doesn't exist on `element_type`.  
  
## Fix  
  
The fix strips typedefs first, then checks if the underlying type is a pointer. If the pointer's target type is `element_type`, it dereferences and extracts the `p` member:  
  
```python  
def maybe_unwrap_foa_element(e):  
    underlying_type = e.type.strip_typedefs()  
    # Handle pointer to element_type (from iterating elements array)  
    if underlying_type.code == gdb.TYPE_CODE_PTR:  
        target_type = f"{underlying_type.target().strip_typedefs()}"  
        if target_type.startswith("boost::unordered::detail::foa::element_type<"):  
            return e.dereference()["p"]  
    # Handle element_type directly  
    elif f"{underlying_type}".startswith("boost::unordered::detail::foa::element_type<"):  
        return e["p"]  
    return e  
```  
  
## Affected Containers  
  
All node-based FOA containers that use `element_type` for indirection:  
- `boost::unordered::unordered_node_map`  
- `boost::unordered::unordered_node_set`  
- `boost::unordered::concurrent_node_map`  
- `boost::unordered::concurrent_node_set`  
  
## Files to Update  
  
1. `libs/unordered/extra/boost_unordered_printers.py` - the source Python script  
2. `boost/unordered/detail/unordered_printers.hpp` - the embedded version (regenerate from the Python script)  
  
## Test Case  
  
```cpp  
#include <boost/unordered/unordered_node_map.hpp>  
#include <string>  
  
int main() {  
    boost::unordered_node_map<int, std::string> orders;  
    orders[1] = "first";  
    orders[2] = "second";  
    return 0;  // breakpoint here  
}  
```  
  
Before fix:  
```  
(gdb) p orders  
$1 = boost::unordered_node_map with 2 elementsPython Exception <class 'gdb.error'>: There is no member or method named first.  
```  
  
After fix:  
```  
(gdb) p orders  
$1 = boost::unordered_node_map with 2 elements = {  
  [2] = "second",  
  [1] = "first"  
}  
```

---

## Comment 6

> Username: k3DW  
> Created at: 2026-01-23 23:35:35 UTC  
> Url: https://github.com/boostorg/unordered/issues/340#issuecomment-3793016783  

Got it, thanks for the head start. I was able to reproduce it on my end. It turns out the `-std=c++26` flag was load-bearing for this error, and I omitted that flag. I'll look into it.
