# #461 - Build error on macOS and NetBSD: `close_handles.cpp: error: expected id-expression before '(' token` [Closed]

> Username: barracuda156  
> Created at: 2025-01-26 21:03:17 UTC  
> Updated at: 2025-03-05 08:38:27 UTC  
> Closed at: 2025-03-05 08:38:27 UTC  
> Url: https://github.com/boostorg/process/issues/461  

This is the last unfixed issue, I guess, for 1.87:  
  
```  
:info:build darwin.compile.c++ bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/posix/close_handles.o  
:info:build     "/opt/local/bin/g++-mp-14"   -fvisibility-inlines-hidden -Os -std=gnu++17 -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_NO_LIB=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_LIB=1 -DDATE_TIME_INLINE -DNDEBUG   -I"." -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/asio/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/assert/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/atomic/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/container_hash/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/context/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/core/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/describe/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/detail/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/filesystem/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/function_types/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/iterator/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/move/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/mp11/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/optional/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/predef/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/scope/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/smart_ptr/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/system/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/throw_exception/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/type_index/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/variant2/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/winapi/include"  -c -o "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/posix/close_handles.o" "/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/posix/close_handles.cpp"  
:info:build In file included from /opt/local/include/LegacySupport/dirent.h:27,  
:info:build                  from /opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/posix/close_handles.cpp:88:  
:info:build /opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/posix/close_handles.cpp: In function 'void boost::process::v2::posix::detail::close_all(const std::vector<int>&, boost::system::error_code&)':  
:info:build /opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/posix/close_handles.cpp:188:21: error: expected id-expression before '(' token  
:info:build   188 |     auto dir_fd = ::dirfd(dir.get());  
:info:build       |                     ^~~~~  
:info:build ...failed darwin.compile.c++ bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/posix/close_handles.o...  
```

---

## Comment 1

> Username: barracuda156  
> Created at: 2025-01-27 09:41:53 UTC  
> Url: https://github.com/boostorg/process/issues/461#issuecomment-2615267979  

Ok, probably just this: https://github.com/NetBSD/pkgsrc/blob/bd41f9e7a95d31be1000782410836edfe63abe62/meta-pkgs/boost/patches/patch-libs_process_src_posix_close__handles.cpp  
  
https://developer.apple.com/library/archive/documentation/System/Conceptual/ManPages_iPhoneOS/man3/dirfd.3.html

---

## Comment 2

> Username: barracuda156  
> Created at: 2025-01-27 10:49:53 UTC  
> Url: https://github.com/boostorg/process/issues/461#issuecomment-2615419121  

Yeah, the fix from NetBSD works.  
  
Now linking fails due to this issue: https://github.com/boostorg/process/issues/453

---

## Comment 3

> Username: barracuda156  
> Created at: 2025-02-26 15:31:11 UTC  
> Url: https://github.com/boostorg/process/issues/461#issuecomment-2685419293  

@klemens-morgenstern Has the fix been merged?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2025-02-26 15:48:04 UTC  
> Url: https://github.com/boostorg/process/issues/461#issuecomment-2685466494  

Nope, but the PR is still open.
