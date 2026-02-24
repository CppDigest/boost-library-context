# #202 Expose stacktrace libraries as build features [Merged]

> Username: uilianries  
> Created at: 2025-01-16 12:08:43 UTC  
> Updated at: 2025-01-18 10:26:48 UTC  
> Merged at: 2025-01-18 10:26:22 UTC  
> Closed at: 2025-01-18 10:26:22 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/202  

Hello! As I commented in the issue #195, this PR exposes all stacktrace libraries as boost features, mirroring what's available in the CMakeLists.txt in this project. So users will be able to build or not specific libraries present in this project. Here are some keypoints to evaluate these new changes:  
  
- Added `build-stacktrace-feature` as generic rule validate on/off entry from users for each feature  
- For `addr2line` I added a rule to disable in case using Windows and not Cygwin. It reflects the rule present in CMakeLists.txt: https://github.com/boostorg/stacktrace/blob/develop/CMakeLists.txt#L67  
  
close #195

---

## Comment 1

> Username: apolukhin  
> Created_at: 2025-01-16 13:15:52 UTC  
> Updated_at: 2025-01-16 13:16:55 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/202#issuecomment-2595632800  

CI breaks with the following message:  
```  
 clang-linux.compile.c++ bin.v2/libs/stacktrace/test/from_exception_disabled_none_ho.test/clang-linux-15/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/test_from_exception_none.o  
clang-linux.link bin.v2/libs/stacktrace/test/from_exception_disabled_none_ho.test/clang-linux-15/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/from_exception_disabled_none_ho  
/usr/bin/ld: bin.v2/libs/stacktrace/test/from_exception_disabled_none_ho.test/clang-linux-15/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/from_exception_disabled_none_ho: hidden symbol `_ZN5boost10stacktrace6detail18this_thread_frames7collectEPPKvmm' in bin.v2/libs/stacktrace/test/from_exception_disabled_none_ho.test/clang-linux-15/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/test_from_exception_none.o is referenced by DSO  
/usr/bin/ld: final link failed: bad value  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
Same for GCC:  
```  
gcc.compile.c++ bin.v2/libs/stacktrace/test/from_exception_disabled_bt_ho.test/gcc-9/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/test_from_exception_none.o  
gcc.link bin.v2/libs/stacktrace/test/from_exception_disabled_bt_ho.test/gcc-9/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/from_exception_disabled_bt_ho  
/usr/bin/ld: bin.v2/libs/stacktrace/build/gcc-9/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/libboost_stacktrace_from_exception.so.1.88.0: undefined reference to `boost::stacktrace::detail::this_thread_frames::collect(void const**, unsigned long, unsigned long)'  
collect2: error: ld returned 1 exit status  
  
    "g++-9"  -Wl,-rpath -Wl,"/home/runner/work/stacktrace/boost-root/bin.v2/libs/stacktrace/build/gcc-9/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden" -Wl,-rpath-link -Wl,"/home/runner/work/stacktrace/boost-root/bin.v2/libs/stacktrace/build/gcc-9/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden" -o "bin.v2/libs/stacktrace/test/from_exception_disabled_bt_ho.test/gcc-9/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/from_exception_disabled_bt_ho" -Wl,--start-group "bin.v2/libs/stacktrace/test/from_exception_disabled_bt_ho.test/gcc-9/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/test_from_exception_none.o" "bin.v2/libs/stacktrace/build/gcc-9/release/x86_64/cxxstd-2a-iso/threading-multi/visibility-hidden/libboost_stacktrace_from_exception.so.1.88.0"  -Wl,-Bstatic -lbacktrace -Wl,-Bdynamic -lrt -ldl -Wl,--end-group -fPIC -m64 -pthread -std=c++2a -fvisibility=hidden -fvisibility-inlines-hidden -lpthread  
```

---

## Comment 2

> Username: uilianries  
> Created_at: 2025-01-16 13:17:19 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/202#issuecomment-2595640227  

@apolukhin Thank you for informing, I'll check it. I only tested on Linux-GCC (locally) so far.

---

## Comment 3

> Username: uilianries  
> Created_at: 2025-01-17 06:37:38 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/202#issuecomment-2597538752  

@apolukhin Could please run the CI again. I just updated the code to avoid injecting those common constants to all libraries. Locally, I was able to reproduce the CI error and mitigate it by reverting that.

---

## Comment 4

> Username: coveralls  
> Created_at: 2025-01-17 14:37:56 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/202#issuecomment-2598509418  

[![Coverage Status](https://coveralls.io/builds/71791058/badge)](https://coveralls.io/builds/71791058)  
  
coverage: 86.371%. remained the same  
when pulling **b0d687cf91051051dfcdc96385e13c31fb48ece3 on uilianries:b2/lib-options**  
into **ea282324b8b8623bf0515248b525650a6e78ebdd on boostorg:develop**.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2025-01-18 10:26:47 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/202#issuecomment-2599660953  

Many thanks for the PR!

---
