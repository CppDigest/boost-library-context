# #321 - Warnings/Errors on Windows [Closed]

> Username: Flamefire  
> Created at: 2024-08-25 12:30:02 UTC  
> Updated at: 2024-08-25 18:14:33 UTC  
> Closed at: 2024-08-25 13:13:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/321  

I get a couple issues when compiling on Windows when using Cygwin, MinGW or clang-cl:  
  
```  
libs\filesystem\src\operations.cpp(1594,31): error: comparison of integers of different signs: 'boost::winapi::NTSTATUS_' (aka 'long') and 'DWORD' (aka 'unsigned long') [-Werror,-Wsign-compare]  
    if (BOOST_UNLIKELY(status == STATUS_INVALID_PARAMETER && (obj_attrs.Attributes & OBJ_DONT_REPARSE) != 0u))  
                       ~~~~~~ ^  ~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
```  
libs/filesystem/src/operations.cpp:1404:175: error: cast between incompatible function types from 'boost::winapi::FARPROC_' {aka 'long long int (*)()'} to 'BOOL (*)(HANDLE, boost::filesystem::detail::file_info_by_handle_class, LPVOID, DWORD)' {aka 'int (*)(void*, boost::filesystem::detail::file_info_by_handle_class, void*, long unsigned int)'} [-Werror=cast-function-type]  
         GetFileInformationByHandleEx_t* get_file_information_by_handle_ex = (GetFileInformationByHandleEx_t*)boost::winapi::get_proc_address(h, "GetFileInformationByHandleEx");  
                                                                                                                                                                               ^  
libs/filesystem/src/operations.cpp:1406:166: error: cast between incompatible function types from 'boost::winapi::FARPROC_' {aka 'long long int (*)()'} to 'BOOL (*)(HANDLE, boost::filesystem::detail::file_info_by_handle_class, LPVOID, DWORD)' {aka 'int (*)(void*, boost::filesystem::detail::file_info_by_handle_class, void*, long unsigned int)'} [-Werror=cast-function-type]  
         SetFileInformationByHandle_t* set_file_information_by_handle = (SetFileInformationByHandle_t*)boost::winapi::get_proc_address(h, "SetFileInformationByHandle");  
                                                                                                                                                                      ^  
libs/filesystem/src/operations.cpp:1408:144: error: cast between incompatible function types from 'boost::winapi::FARPROC_' {aka 'long long int (*)()'} to 'BOOL (*)(LPCWSTR, LPCWSTR, LPSECURITY_ATTRIBUTES)' {aka 'int (*)(const wchar_t*, const wchar_t*, _SECURITY_ATTRIBUTES*)'} [-Werror=cast-function-type]  
         filesystem::detail::atomic_store_relaxed(create_hard_link_api, (CreateHardLinkW_t*)boost::winapi::get_proc_address(h, "CreateHardLinkW"));  
                                                                                                                                                ^  
libs/filesystem/src/operations.cpp:1409:156: error: cast between incompatible function types from 'boost::winapi::FARPROC_' {aka 'long long int (*)()'} to 'BOOLEAN (*)(LPCWSTR, LPCWSTR, DWORD)' {aka 'unsigned char (*)(const wchar_t*, const wchar_t*, long unsigned int)'} [-Werror=cast-function-type]  
         filesystem::detail::atomic_store_relaxed(create_symbolic_link_api, (CreateSymbolicLinkW_t*)boost::winapi::get_proc_address(h, "CreateSymbolicLinkW"));  
                                                                                                                                                            ^  
libs/filesystem/src/operations.cpp:1422:136: error: cast between incompatible function types from 'boost::winapi::FARPROC_' {aka 'long long int (*)()'} to 'boost::winapi::NTSTATUS_ (*)(PHANDLE, ACCESS_MASK, boost::filesystem::detail::object_attributes*, boost::filesystem::detail::io_status_block*, PLARGE_INTEGER, ULONG, ULONG, ULONG, ULONG, PVOID, ULONG)' {aka 'long int (*)(void**, long unsigned int, boost::filesystem::detail::object_attributes*, boost::filesystem::detail::io_status_block*, _LARGE_INTEGER*, long unsigned int, long unsigned int, long unsigned int, long unsigned int, void*, long unsigned int)'} [-Werror=cast-function-type]  
         filesystem::detail::atomic_store_relaxed(nt_create_file_api, (NtCreateFile_t*)boost::winapi::get_proc_address(h, "NtCreateFile"));  
                                                                                                                                        ^  
libs/filesystem/src/operations.cpp:1423:161: error: cast between incompatible function types from 'boost::winapi::FARPROC_' {aka 'long long int (*)()'} to 'boost::winapi::NTSTATUS_ (*)(HANDLE, HANDLE, boost::filesystem::detail::pio_apc_routine, PVOID, boost::filesystem::detail::io_status_block*, PVOID, ULONG, boost::filesystem::detail::file_information_class, BOOLEAN, boost::filesystem::detail::unicode_string*, BOOLEAN)' {aka 'long int (*)(void*, void*, void (*)(void*, boost::filesystem::detail::io_status_block*, long unsigned int), void*, boost::filesystem::detail::io_status_block*, void*, long unsigned int, boost::filesystem::detail::file_information_class, unsigned char, boost::filesystem::detail::unicode_string*, unsigned char)'} [-Werror=cast-function-type]  
         filesystem::detail::atomic_store_relaxed(nt_query_directory_file_api, (NtQueryDirectoryFile_t*)boost::winapi::get_proc_address(h, "NtQueryDirectoryFile"));  
                                                                                                                                                                 ^  
```  
  
Could those be fixed and maybe make the libray build with `-Wall -Werror`? I.e. in B2/Jam:  
```  
  <warnings>all  
  <warnings-as-errors>on  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2024-08-25 13:16:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/321#issuecomment-2308833630  

I disabled the signed/unsigned mismatch warnings. I cannot see the second kind of warnings in the CI, so I'm assuming you're doing something that triggers them. In that case, please stop doing this.  
  
> Could those be fixed and maybe make the libray build with `-Wall -Werror`?  
  
I'm not supporting building the library with zero warnings, let alone in "warnings as errors" mode. If you're building Boost.Filesystem like this, don't do this.

---

## Comment 2

> Username: Flamefire  
> Created at: 2024-08-25 18:14:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/321#issuecomment-2308947286  

> I disabled the signed/unsigned mismatch warnings. I cannot see the second kind of warnings in the CI, so I'm assuming you're doing something that triggers them. In that case, please stop doing this.  
  
The warning gets enabled by `-Wextra`, I created #323 and https://github.com/boostorg/winapi/pull/95 to avoid them.     
I ran into this as the "modular b2-boost build" seemingly propagates warnings to dependencies. That should be fixed there too but the fix is pretty easy and you merged a similar fix in Boost.Thread earlier: https://github.com/boostorg/thread/pull/227  
  
> > Could those be fixed and maybe make the libray build with `-Wall -Werror`?  
>   
> I'm not supporting building the library with zero warnings, let alone in "warnings as errors" mode. If you're building Boost.Filesystem like this, don't do this.  
  
I suggested the "warnings as errors" mode only to ensure CI coverage of the "warning-free feature".  I found `-Wall` useful as it doesn't has too many false-positives and users are likely to have it enabled.     
But I do admit it can be annoying to work around those. It might still be useful to selectively enable some warnings, e.g. after fixing the signed-unsigned warning it might make sense to enable (only) that warning and `-Werror` to avoid regressions.
