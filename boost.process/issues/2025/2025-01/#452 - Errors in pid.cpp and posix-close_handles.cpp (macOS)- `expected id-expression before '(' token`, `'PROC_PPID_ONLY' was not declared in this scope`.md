# #452 - Errors in pid.cpp and posix/close_handles.cpp (macOS): `expected id-expression before '(' token`, `'PROC_PPID_ONLY' was not declared in this scope` [Closed]

> Username: barracuda156  
> Created at: 2025-01-17 06:40:31 UTC  
> Updated at: 2025-04-14 15:54:46 UTC  
> Closed at: 2025-04-14 15:54:46 UTC  
> Url: https://github.com/boostorg/process/issues/452  

```  
:info:build darwin.compile.c++ bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/pid.o  
:info:build     "/opt/local/bin/g++-mp-14"   -fvisibility-inlines-hidden -Os -std=gnu++17 -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_NO_LIB=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_LIB=1 -DDATE_TIME_INLINE -DNDEBUG   -I"." -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/asio/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/assert/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/atomic/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/container_hash/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/context/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/core/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/describe/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/detail/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/filesystem/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/function_types/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/iterator/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/move/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/mp11/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/optional/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/predef/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/process/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/scope/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/smart_ptr/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/system/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/throw_exception/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/type_index/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/variant2/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/winapi/include"  -c -o "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/pid.o" "/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/pid.cpp"  
:info:build /opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/pid.cpp: In function 'std::vector<int> boost::process::v2::child_pids(pid_type, boost::system::error_code&)':  
:info:build /opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/pid.cpp:181:30: error: 'PROC_PPID_ONLY' was not declared in this scope; did you mean 'PROC_RUID_ONLY'?  
:info:build   181 |     vec.resize(proc_listpids(PROC_PPID_ONLY, (uint32_t)pid, nullptr, 0) / sizeof(pid_type));  
:info:build       |                              ^~~~~~~~~~~~~~  
:info:build       |                              PROC_RUID_ONLY  
:info:build ...failed darwin.compile.c++ bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/pid.o...  
:info:build darwin.compile.c++ bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/posix/close_handles.o  
:info:build     "/opt/local/bin/g++-mp-14"   -fvisibility-inlines-hidden -Os -std=gnu++17 -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_NO_LIB=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_LIB=1 -DDATE_TIME_INLINE -DNDEBUG   -I"." -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/asio/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/assert/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/atomic/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/container_hash/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/context/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/core/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/describe/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/detail/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/filesystem/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/function_types/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/iterator/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/move/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/mp11/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/optional/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/predef/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/process/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/scope/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/smart_ptr/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/system/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/throw_exception/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/type_index/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/variant2/include" -I"/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/winapi/include"  -c -o "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/posix/close_handles.o" "/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/posix/close_handles.cpp"  
:info:build In file included from /opt/local/include/LegacySupport/dirent.h:27,  
:info:build                  from /opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/posix/close_handles.cpp:88:  
:info:build /opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/posix/close_handles.cpp: In function 'void boost::process::v2::posix::detail::close_all(const std::vector<int>&, boost::system::error_code&)':  
:info:build /opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/posix/close_handles.cpp:188:21: error: expected id-expression before '(' token  
:info:build   188 |     auto dir_fd = ::dirfd(dir.get());  
:info:build       |                     ^~~~~  
```  
  
There are two issues here:  
  
1. `PROC_PPID_ONLY` appears in 10.7 SDK, so should be used conditionally:  
https://github.com/phracker/MacOSX-SDKs/blob/041600eda65c6a668f66cb7d56b7d1da3e8bcc93/MacOSX10.6.sdk/usr/include/sys/proc_info.h  
vs  
https://github.com/phracker/MacOSX-SDKs/blob/041600eda65c6a668f66cb7d56b7d1da3e8bcc93/MacOSX10.7.sdk/usr/include/sys/proc_info.h#L53  
  
2. Something got broken about `auto dir_fd = ::dirfd(dir.get());` relatively recently. The chunk was added in Boost 1.80, it seems: https://github.com/boostorg/process/commit/062ac9beb2ef04f15f735d18be774a345a0bf814  
I can say for sure that Boost 1.81 builds fine and works on my system, where Boost 1.87 does not. So looks like some later additions or modifications broken it as a side effect. Not yet sure what exactly.

---

## Comment 1

> Username: barracuda156  
> Created at: 2025-01-17 06:50:25 UTC  
> Url: https://github.com/boostorg/process/issues/452#issuecomment-2597552906  

@klemens-morgenstern Could you please have a look, when you get time to? Would be nice to have it fixed. Boost is really important.

---

## Comment 2

> Username: barracuda156  
> Created at: 2025-01-24 23:58:32 UTC  
> Url: https://github.com/boostorg/process/issues/452#issuecomment-2613623080  

@klemens-morgenstern The whole block of code using `PROC_PPID_ONLY` was added in https://github.com/boostorg/process/commit/a3304564c65252fe6cb1a249a2f5c41d83f399e6 so there is no earlier version of it, apparently. The only way to make this compilable is to modify the existing one.  
I am not familiar with process management implementation details, unfortunately. Will some more generic version for `child_pids` work on macOS versions without support for `PROC_PPID_ONLY`?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-01-26 13:49:40 UTC  
> Url: https://github.com/boostorg/process/issues/452#issuecomment-2614432563  

Does 0ca663c8 do the trick?

---

## Comment 4

> Username: barracuda156  
> Created at: 2025-01-26 16:24:14 UTC  
> Url: https://github.com/boostorg/process/issues/452#issuecomment-2614492771  

@klemens-morgenstern Thank you very much, I will try this today and let you know.

---

## Comment 5

> Username: barracuda156  
> Created at: 2025-01-26 20:31:23 UTC  
> Url: https://github.com/boostorg/process/issues/452#issuecomment-2614580399  

@klemens-morgenstern Yes, https://github.com/boostorg/process/commit/0ca663c8262b65ccee9a4e6abbafaef710f6b36f fixes `PROC_PPID_ONLY` issue. Thank you!  
  
`auto dir_fd = ::dirfd(dir.get())` still fails, but perhaps it should be a separate issue, these are unrelated.

---

## Comment 6

> Username: barracuda156  
> Created at: 2025-01-26 21:05:02 UTC  
> Updated at: 2025-01-26 21:11:07 UTC  
> Url: https://github.com/boostorg/process/issues/452#issuecomment-2614590705  

@klemens-morgenstern This issue can be closed once https://github.com/boostorg/process/commit/0ca663c8262b65ccee9a4e6abbafaef710f6b36f is merged to the master.  
  
I have opened a separate issue for the remaining error: https://github.com/boostorg/process/issues/461  
BTW, the same error on NetBSD: https://github.com/boostorg/process/issues/416
