# #306 - new_allocator.hpp error: '__cpp_sized_deallocation' is not defined, evaluates to 0 [-Werror,-Wundef] [Closed]

> Username: alex-tee  
> Created at: 2025-07-24 01:36:41 UTC  
> Updated at: 2025-09-06 10:22:09 UTC  
> Closed at: 2025-09-06 10:22:09 UTC  
> Url: https://github.com/boostorg/container/issues/306  

On MacOS I get the following build warning/error:  
  
```  
    /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang -x c++ -ivfsstatcache /var/folders/yh/z9ply7ds43z9gx75pn61x53h0000gn/C/com.apple.DeveloperTools/16.3-16E140/Xcode/SDKStatCaches.noindex/macosx15.4-24E241-88b860576fb364319593bd8fb30666b0.sdkstatcache -fmessage-length\=0 -fdiagnostics-show-note-include-stack -fmacro-backtrace-limit\=0 -fno-color-diagnostics -Wno-trigraphs -Wno-missing-field-initializers -Wno-missing-prototypes -Wno-return-type -Wno-non-virtual-dtor -Wno-overloaded-virtual -Wno-exit-time-destructors -Wno-missing-braces -Wparentheses -Wswitch -Wno-unused-function -Wno-unused-label -Wno-unused-parameter -Wno-unused-variable -Wunused-value -Wno-empty-body -Wno-uninitialized -Wno-unknown-pragmas -Wno-shadow -Wno-four-char-constants -Wno-conversion -Wno-constant-conversion -Wno-int-conversion -Wno-bool-conversion -Wno-enum-conversion -Wno-float-conversion -Wno-non-literal-null-conversion -Wno-objc-literal-conversion -Wshorten-64-to-32 -Wno-newline-eof -Wno-c++11-extensions -Wno-implicit-fallthrough -fstrict-aliasing -Wdeprecated-declarations -Winvalid-offsetof -Wno-sign-conversion -Wno-infinite-recursion -Wno-move -Wno-comma -Wno-block-capture-autoreleasing -Wno-strict-prototypes -Wno-range-loop-analysis -Wno-semicolon-before-method-body -Wall -Wextra -Wformat\=2 -Wno-missing-field-initializers -Wno-unused-parameter -Wno-cast-function-type -Wno-attributes -Werror\=alloca -Werror\=disabled-optimization -Werror\=pointer-arith -Werror\=enum-conversion -Werror\=overlength-strings -Werror\=int-to-pointer-cast -Werror\=shadow -Werror\=address -Werror\=undef -Werror\=unused -Werror\=vla -fstrict-aliasing -Wstrict-aliasing\=2 -Werror\=strict-aliasing -Werror\=strict-overflow -Wstrict-overflow\=2 -Werror\=init-self -Werror\=write-strings -Werror\=sign-compare -Werror\=float-conversion -Werror\=uninitialized -Werror\=return-type -Werror\=switch -Werror\=overflow -Werror\=array-bounds -Werror\=enum-compare -Werror\=misleading-indentation -Werror\=int-in-bool-context -Werror\=type-limits -Werror\=endif-labels -Werror\=logical-not-parentheses -Werror\=parentheses -Werror\=comment -Werror\=sizeof-pointer-div -Werror\=shift-count-overflow -Werror\=free-nonheap-object -Werror\=nonnull -Werror\=tautological-compare -Werror\=unused-result -Werror\=inline -Werror\=sizeof-array-argument -Werror\=odr -Werror\=narrowing -Werror\=reorder -Werror\=sequence-point -Werror\=invalid-constexpr -Werror\=pessimizing-move -Werror\=pedantic @/Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/build/zrythm_gui_lib.build/Debug/Objects-normal/x86_64/82b82416624d2658e5098eb0a28c15c5-common-args.resp -include /Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/build/SharedPrecompiledHeaders/SharedPrecompiledHeaders/16716339675839913037/cmake_pch.hxx -MMD -MT dependencies -MF /Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/build/zrythm_gui_lib.build/Debug/Objects-normal/x86_64/unity_4_cxx.d --serialize-diagnostics /Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/build/zrythm_gui_lib.build/Debug/Objects-normal/x86_64/unity_4_cxx.dia -c /Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/src/CMakeFiles/zrythm_gui_lib.dir/Unity/unity_4_cxx.cxx -o /Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/build/zrythm_gui_lib.build/Debug/Objects-normal/x86_64/unity_4_cxx.o  
In file included from /Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/src/CMakeFiles/zrythm_gui_lib.dir/Unity/unity_4_cxx.cxx:10:  
In file included from /Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/src/structure/tracks/track_processor.cpp:16:  
In file included from /Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/_deps/boost-src/libs/container/include/boost/container/static_vector.hpp:26:  
In file included from /Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/_deps/boost-src/libs/container/include/boost/container/vector.hpp:28:  
/Users/alex/builds/KydKbRm4x/0/zrythm/zrythm/build_mac_arm-Debug/_deps/boost-src/libs/container/include/boost/container/new_allocator.hpp:166:12: error: '__cpp_sized_deallocation' is not defined, evaluates to 0 [-Werror,-Wundef]  
  166 |       # if __cpp_sized_deallocation  
      |            ^  
```  
  
Version: 1.88.0  
  
There probably should be a test for `#if defined(__cpp_sized_deallocation)` there before checking its value.

---

## Comment 1

> Username: nigels-com  
> Created at: 2025-09-05 05:57:45 UTC  
> Url: https://github.com/boostorg/container/issues/306#issuecomment-3257160075  

Version 1.89.0  
  
gcc 13.3.0 defines `__cpp_sized_deallocation` for C++14 onwards.  
  
However. clang does not define `__cpp_sized_deallocation` unless [-fsized-deallocation](https://bcain-llvm.readthedocs.io/projects/clang/en/release_37/ReleaseNotes/#new-compiler-flags) is specified.  
  
I agree that this logic should be `#if defined` rather than bare `#if`

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-09-06 10:22:09 UTC  
> Url: https://github.com/boostorg/container/issues/306#issuecomment-3261769830  

Resolved with pull #316, many thanks for the report.
