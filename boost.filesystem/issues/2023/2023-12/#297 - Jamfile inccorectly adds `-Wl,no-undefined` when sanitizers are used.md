# #297 - Jamfile inccorectly adds `-Wl,no-undefined` when sanitizers are used [Closed]

> Username: cmazakas  
> Created at: 2023-12-15 23:48:02 UTC  
> Updated at: 2023-12-16 00:47:57 UTC  
> Closed at: 2023-12-16 00:47:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/297  

OS: Ubuntu 23.10  
clang-17 installed via `apt install -y clang-17`  
  
Using the latest tip of develop, the following command fails:  
```bash  
exbigboss@exbigboss-ubuntu:~/cpp/boost-root$ clear && ./b2 -q libs/filesystem/test undefined-sanitizer=norecover variant=release link=shared toolset=clang-17  
```  
with:  
```bash  
/usr/bin/ld: bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/utf8_codecvt_facet.o: in function `boost::filesystem::detail::utf8_codecvt_facet::do_length(__mbstate_t const&, char const*, char const*, unsigned long) const':  
utf8_codecvt_facet.cpp:(.text._ZNK5boost10filesystem6detail18utf8_codecvt_facet9do_lengthERK11__mbstate_tPKcS7_m[_ZNK5boost10filesystem6detail18utf8_codecvt_facet9do_lengthERK11__mbstate_tPKcS7_m]+0x5f): undefined reference to `__ubsan_vptr_type_cache'  
/usr/bin/ld: utf8_codecvt_facet.cpp:(.text._ZNK5boost10filesystem6detail18utf8_codecvt_facet9do_lengthERK11__mbstate_tPKcS7_m[_ZNK5boost10filesystem6detail18utf8_codecvt_facet9do_lengthERK11__mbstate_tPKcS7_m]+0x9b): undefined reference to `__ubsan_handle_dynamic_type_cache_miss_abort'  
clang++-17: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++-17"    -o "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/libboost_filesystem.so.1.84.0" -Wl,-soname -Wl,"libboost_filesystem.so.1.84.0" -shared -Wl,--start-group "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/codecvt_error_category.o" "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/exception.o" "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/directory.o" "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/operations.o" "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/path.o" "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/path_traits.o" "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/portability.o" "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/unique_path.o" "bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/utf8_codecvt_facet.o" "bin.v2/libs/atomic/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/libboost_atomic.so.1.84.0"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -fvisibility=hidden -fvisibility-inlines-hidden -fsanitize=undefined -fno-sanitize-recover=undefined -fno-omit-frame-pointer --target=x86_64-pc-linux -Wl,--no-undefined  
  
...failed clang-linux.link.dll bin.v2/libs/filesystem/build/clang-linux-17/release/threading-multi/undefined-sanitizer-norecover/visibility-hidden/libboost_filesystem.so.1.84.0...  
```  
  
There are many similar errors:  
```bash  
/usr/bin/ld: utf8_codecvt_facet.cpp:(.text+0x812): undefined reference to `__ubsan_handle_pointer_overflow_abort'  
/usr/bin/ld: utf8_codecvt_facet.cpp:(.text+0x823): undefined reference to `__ubsan_handle_add_overflow_abort'  
/usr/bin/ld: utf8_codecvt_facet.cpp:(.text+0x832): undefined reference to `__ubsan_handle_pointer_overflow_abort'  
/usr/bin/ld: utf8_codecvt_facet.cpp:(.text+0x841): undefined reference to `__ubsan_handle_type_mismatch_v1_abort'  
/usr/bin/ld: utf8_codecvt_facet.cpp:(.text+0x856): undefined reference to `__ubsan_handle_pointer_overflow_abort'  
/usr/bin/ld: utf8_codecvt_facet.cpp:(.text+0x869): undefined reference to `__ubsan_handle_pointer_overflow_abort'  
/usr/bin/ld: utf8_codecvt_facet.cpp:(.text+0x87e): undefined reference to `__ubsan_handle_shift_out_of_bounds_abort'  
/usr/bin/ld: utf8_codecvt_facet.cpp:(.text+0x88a): undefined reference to `__ubsan_handle_pointer_overflow_abort'  
```  
  
I believe this is because of Filesystem's Jamfile, CMake-based builds don't seem affected.  
  
Note a similar issue here: https://github.com/mesonbuild/meson/issues/3853

---

## Comment 1

> Username: Lastique  
> Created at: 2023-12-16 00:47:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/297#issuecomment-1858645357  

This is a clang bug reported [here](https://github.com/llvm/llvm-project/issues/60578). You can work around it by explicitly linking with `libubsan`. For example, add `linkflags=-lubsan` to the `b2` command line.
