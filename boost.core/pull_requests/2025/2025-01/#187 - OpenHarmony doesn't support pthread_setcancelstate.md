# #187 OpenHarmony doesn't support pthread_setcancelstate. [Merged]

> Username: eagleoflqj  
> Created at: 2025-01-08 02:19:15 UTC  
> Updated at: 2025-01-08 14:47:03 UTC  
> Merged at: 2025-01-08 14:45:11 UTC  
> Closed at: 2025-01-08 14:45:12 UTC  
> Url: https://github.com/boostorg/core/pull/187  

Like Android, OpenHarmony doesn't support pthread_setcancelstate as well. The macro `__OHOS__` can be found in [llvm](https://github.com/llvm/llvm-project/blob/30ba8be22eb0e3f771624f6f47229129cf74e976/clang/lib/Basic/Targets/OSTargets.h#L989).  
  
<details>  
<code>  
[4/5] Building CXX object libs/iostreams/CMakeFiles/boost_iostreams.dir/src/mapped_file.cpp.o  
FAILED: libs/iostreams/CMakeFiles/boost_iostreams.dir/src/mapped_file.cpp.o   
/tmp/command-line-tools/sdk/default/openharmony/native/llvm/bin/clang++ --target=x86_64-linux-ohos --gcc-toolchain=/tmp/command-line-tools/sdk/default/openharmony/native/llvm --sysroot=/tmp/command-line-tools/sdk/default/openharmony/native/sysroot -DBOOST_IOSTREAMS_NO_LIB -DBOOST_IOSTREAMS_STATIC_LINK -DBOOST_RANDOM_NO_LIB -DBOOST_RANDOM_STATIC_LINK -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/iostreams/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/assert/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/config/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/core/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/static_assert/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/throw_exception/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/detail/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/preprocessor/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/type_traits/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/function/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/bind/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/integer/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/iterator/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/concept_check/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/function_types/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/mpl/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/predef/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/utility/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/io/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/fusion/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/container_hash/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/describe/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/mp11/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/tuple/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/typeof/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/functional/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/optional/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/move/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/smart_ptr/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/random/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/array/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/dynamic_bitset/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/range/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/conversion/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/regex/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/system/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/variant2/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/winapi/include -I/mnt/storage/github/fcitx5-prebuilder/boost/libs/numeric/conversion/include -fdata-sections -ffunction-sections -funwind-tables -fstack-protector-strong -no-canonical-prefixes -fno-addrsig -Wa,--noexecstack -Wformat -Werror=format-security   -D__MUSL__ -O2 -DNDEBUG  -fPIC -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/iostreams/CMakeFiles/boost_iostreams.dir/src/mapped_file.cpp.o -MF libs/iostreams/CMakeFiles/boost_iostreams.dir/src/mapped_file.cpp.o.d -o libs/iostreams/CMakeFiles/boost_iostreams.dir/src/mapped_file.cpp.o -c /mnt/storage/github/fcitx5-prebuilder/boost/libs/iostreams/src/mapped_file.cpp  
clang++: warning: argument unused during compilation: '--gcc-toolchain=/tmp/command-line-tools/sdk/default/openharmony/native/llvm' [-Wunused-command-line-argument]  
In file included from /mnt/storage/github/fcitx5-prebuilder/boost/libs/iostreams/src/mapped_file.cpp:19:  
In file included from /mnt/storage/github/fcitx5-prebuilder/boost/libs/iostreams/include/boost/iostreams/device/mapped_file.hpp:29:  
In file included from /mnt/storage/github/fcitx5-prebuilder/boost/libs/smart_ptr/include/boost/shared_ptr.hpp:17:  
In file included from /mnt/storage/github/fcitx5-prebuilder/boost/libs/smart_ptr/include/boost/smart_ptr/shared_ptr.hpp:30:  
In file included from /mnt/storage/github/fcitx5-prebuilder/boost/libs/smart_ptr/include/boost/smart_ptr/detail/spinlock_pool.hpp:25:  
In file included from /mnt/storage/github/fcitx5-prebuilder/boost/libs/smart_ptr/include/boost/smart_ptr/detail/spinlock.hpp:42:  
In file included from /mnt/storage/github/fcitx5-prebuilder/boost/libs/smart_ptr/include/boost/smart_ptr/detail/spinlock_gcc_atomic.hpp:14:  
In file included from /mnt/storage/github/fcitx5-prebuilder/boost/libs/smart_ptr/include/boost/smart_ptr/detail/yield_k.hpp:22:  
In file included from /mnt/storage/github/fcitx5-prebuilder/boost/libs/core/include/boost/core/yield_primitives.hpp:10:  
/mnt/storage/github/fcitx5-prebuilder/boost/libs/core/include/boost/core/detail/sp_thread_sleep.hpp:73:5: error: use of undeclared identifier 'pthread_setcancelstate'  
    pthread_setcancelstate( PTHREAD_CANCEL_DISABLE, &oldst );  
    ^  
/mnt/storage/github/fcitx5-prebuilder/boost/libs/core/include/boost/core/detail/sp_thread_sleep.hpp:90:5: error: use of undeclared identifier 'pthread_setcancelstate'  
    pthread_setcancelstate( oldst, &oldst );  
    ^  
2 errors generated.  
ninja: build stopped: subcommand failed.  
</code>  
</details>

---
