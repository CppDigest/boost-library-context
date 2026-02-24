# #55 - Symbols file for stacktrace is not loaded after deployment in Windows [Closed]

> Username: jzmaddock  
> Created at: 2018-05-10 16:44:59 UTC  
> Updated at: 2021-12-02 16:21:14 UTC  
> Closed at: 2020-01-23 20:56:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/55  

Moved from https://svn.boost.org/trac10/ticket/13529

---

## Comment 1

> Username: zlojvavan  
> Created at: 2018-05-11 05:24:37 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/55#issuecomment-388263181  

see also https://github.com/boostorg/stacktrace/issues/47#issuecomment-382623499

---

## Comment 2

> Username: emptyVoid  
> Created at: 2019-02-25 15:25:24 UTC  
> Updated at: 2019-02-25 15:26:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/55#issuecomment-467053114  

If anyone else is struggling with this issue, here's a works-for-me workaround:  
* Link applications and dynamic libraries with ```/PDBALTPATH:%_PDB%``` setting ([see docs for details](https://docs.microsoft.com/en-us/cpp/build/reference/pdbaltpath-use-alternate-pdb-path)) -- presumably it works with arbitrary relative or absolute path, though I've tested it with no path information only.  
  
In case application's working directory differs from the PDBs' location and the latter is not known at link stage (e.g. user can change the install location), here's an additional step:  
* Set ```_NT_SYMBOL_PATH``` environment variable to the path to PDBs ([see docs for details](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/symbol-path#controlling-the-symbol-path)). I'm doing it at the application start with something like this:  
```c++  
wchar_t path[MAX_PATH];  
GetModuleFileNameW( nullptr, path, MAX_PATH );  
PathRemoveFileSpecW( path );  
SetEnvironmentVariableW( L"_NT_SYMBOL_PATH", path );  
```

---

## Comment 3

> Username: rogerorr  
> Created at: 2019-11-25 11:51:11 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/55#issuecomment-558121585  

A small change to the work around emptyVoid suggests above is to set `_NT_ALT_SYMBOL_PATH` to the path of the PDBs. This allows you to retain the use of `_NT_SYMBOL_PATH` (eg to symbols from a symbol store), while also finding any PDBs installed next to the application binary.

---

## Comment 4

> Username: rogerorr  
> Created at: 2019-11-30 19:01:41 UTC  
> Updated at: 2019-11-30 19:01:56 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/55#issuecomment-560012122  

I've reported the underlying issue to the Microsoft 'Feedback Hub' using Category "Developer Platform" and Subcategory "Debugging Tools for Windows"  
  
See https://aka.ms/AA6pxh8  
  
Feel free to upvote ...

---

## Comment 5

> Username: apolukhin  
> Created at: 2020-01-23 21:00:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/55#issuecomment-577874833  

Added description of that issue to the docs.   
Many thanks for the links, tweaks and workarounds!

---

## Comment 6

> Username: SpriteOvO  
> Created at: 2021-12-02 16:21:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/55#issuecomment-984782636  

I don't know why the solution provided by @emptyVoid for setting the env variable doesn't work for me with the `RelWithDebInfo` configuration (yes, `pdb` files are in the same directory as the executable, and `Debug` configuration works).  
  
So I had to start trying the linker option `/PDBALTPATH:%_PDB%`, and this is the beginning of a nightmare.  
  
It looks very simple, just `add_link_options("/PDBALTPATH:%_PDB%")`. But I tried for 3 hours and still can't get it to work. Eventually I stumbled upon `CMake` escaping that linker option.  
  
So if you try `set_target_properties(${TARGET_NAME} PROPERTIES LINK_FLAGS "/PDBALTPATH:%_PDB%")`, `CMake` will escape the option to `/PDBALTPATH:%%_PDB%%`,  
and if you try `add_link_options("/PDBALTPATH:%_PDB%")`, `CMake` will escape the option to `/PDBALTPATH:%%%%_PDB%%%%`.  
  
And I couldn't find any mention of the "%" escape in their documentation.  
  
This is the bug report for it:  
https://cmake.org/Bug/view.php?id=15865  
https://gitlab.kitware.com/cmake/cmake/-/issues/15865  
  
Since I didn't find any way to suppress the "%" escape, my workaround is pretty hacky: at the end of the top-level `CMakeLists.txt`, enumerate all targets and manually specify the `pdb` file name for each target so that it does not rely on the "%" character.  
  
Now the library is working for me. Here is the code, hopefully it will save close to 6 hours for some people. 😭  
  
```cmake  
macro(get_all_targets_recursive targets dir)  
    get_property(subdirectories DIRECTORY ${dir} PROPERTY SUBDIRECTORIES)  
    foreach(subdir ${subdirectories})  
        get_all_targets_recursive(${targets} ${subdir})  
    endforeach()  
  
    get_property(current_targets DIRECTORY ${dir} PROPERTY BUILDSYSTEM_TARGETS)  
    list(APPEND ${targets} ${current_targets})  
endmacro()  
  
function(get_all_targets var)  
    set(targets)  
    get_all_targets_recursive(targets ${CMAKE_CURRENT_SOURCE_DIR})  
    set(${var} ${targets} PARENT_SCOPE)  
endfunction()  
  
function(apply_pdbaltpath_pdb_for_all_targets)  
    get_all_targets(ALL_TARGETS)  
    foreach(TARGET_NAME ${ALL_TARGETS})  
        get_target_property(OUTPUT_NAME ${TARGET_NAME} OUTPUT_NAME)  
        if ("${OUTPUT_NAME}" STREQUAL "OUTPUT_NAME-NOTFOUND" OR "${OUTPUT_NAME}" STREQUAL "")  
            set(OUTPUT_NAME ${TARGET_NAME})  
        endif()  
        set_target_properties(${TARGET_NAME} PROPERTIES LINK_FLAGS "/PDBALTPATH:${OUTPUT_NAME}.pdb")  
    endforeach()  
endfunction()  
  
# At the end of the top-level CMakeLists.txt  
apply_pdbaltpath_pdb_for_all_targets()  
```
