# #576 - Test failures on (mostly) macOS [Open]

> Username: pdimov  
> Created at: 2021-06-02 16:42:55 UTC  
> Updated at: 2021-06-23 13:27:26 UTC  
> Url: https://github.com/boostorg/json/issues/576  

https://travis-ci.org/github/boostorg/json/builds/773287795  
  
s390x fails many tests for non-obvious reasons. One thing to note is that it's a big endian platform (the only one such on Travis.)  
  
Xcode 7.3 and 8.3 fail with:  
```  
clang-darwin.compile.c++ bin.v2/libs/json/build/clang-darwin-7.3/debug/address-model-32/cxxstd-11-iso/threading-multi/visibility-hidden/src.o  
In file included from libs/json/src/src.cpp:10:  
In file included from ./boost/json/src.hpp:27:  
In file included from ./boost/json.hpp:15:  
In file included from ./boost/json/array.hpp:16:  
In file included from ./boost/json/storage_ptr.hpp:16:  
./boost/json/detail/default_resource.hpp:84:5: error: constexpr constructor never produces a constant expression [-Winvalid-constexpr]  
    holder()  
    ^  
./boost/json/detail/default_resource.hpp:85:11: note: non-literal type 'boost::json::detail::default_resource' cannot be used in a constant expression  
        : mr()  
          ^  
1 error generated.  
    "clang++" -x c++ -std=c++11 -fvisibility-inlines-hidden -fPIC -m32 -O0 -fno-inline -Wall -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DBOOST_JSON_DYN_LINK=1 -DBOOST_JSON_SOURCE -I"."  -c -o "bin.v2/libs/json/build/clang-darwin-7.3/debug/address-model-32/cxxstd-11-iso/threading-multi/visibility-hidden/src.o" "libs/json/src/src.cpp"  
...failed clang-darwin.compile.c++ bin.v2/libs/json/build/clang-darwin-7.3/debug/address-model-32/cxxstd-11-iso/threading-multi/visibility-hidden/src.o...  
```  
Xcode 9.4 fails with  
```  
clang-darwin.link bin.v2/libs/json/test/doc_quick_look.test/clang-darwin-9.1/debug/cxxstd-11-iso/threading-multi/visibility-hidden/doc_quick_look  
Undefined symbols for architecture x86_64:  
  "std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::basic_string(char const*)", referenced from:  
      boost::json::set3() in doc_quick_look.o  
ld: symbol(s) not found for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
    "clang++"   -o "bin.v2/libs/json/test/doc_quick_look.test/clang-darwin-9.1/debug/cxxstd-11-iso/threading-multi/visibility-hidden/doc_quick_look" "bin.v2/libs/json/test/doc_quick_look.test/clang-darwin-9.1/debug/cxxstd-11-iso/threading-multi/visibility-hidden/doc_quick_look.o" "bin.v2/libs/json/test/doc_quick_look.test/clang-darwin-9.1/debug/cxxstd-11-iso/threading-multi/visibility-hidden/main.o" "bin.v2/libs/json/build/clang-darwin-9.1/debug/cxxstd-11-iso/threading-multi/visibility-hidden/libboost_json.dylib" "bin.v2/libs/container/build/clang-darwin-9.1/debug/cxxstd-11-iso/threading-multi/visibility-hidden/libboost_container.dylib"        -fPIC -m64 -std=c++11 -g -fvisibility=hidden -fvisibility-inlines-hidden  
...failed clang-darwin.link bin.v2/libs/json/test/doc_quick_look.test/clang-darwin-9.1/debug/cxxstd-11-iso/threading-multi/visibility-hidden/doc_quick_look...  
```  
(interestingly, `doc_quick_look` is the only thing that fails to link in this manner.)  
  
It also fails one test with  
```  
testing.capture-output bin.v2/libs/json/test/value.test/clang-darwin-9.1/release/address-model-32/cxxstd-11-iso/threading-multi/visibility-hidden/value.run  
====== BEGIN OUTPUT ======  
boost.json.value  
#630 value.cpp(1476) failed: static_cast<float>(V) == static_cast<float>(jv.to_number<float>())  
#640 value.cpp(1490) failed: static_cast<float>(V) == static_cast<float>(jv.to_number<float>())  
6ms, 1 suites, 2 failures, 980 total.  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
but only in 32 bit mode.  
  
Xcode 10.1 has the same link failure with `doc_quick_look`. It doesn't support 32 bits so the other failure isn't present. :-)

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-06-23 12:19:48 UTC  
> Url: https://github.com/boostorg/json/issues/576#issuecomment-866787358  

I've been investigating a different thing and built a 32-bit mingw version. That second problem is indeed related to 32 bit address model.

---

## Comment 2

> Username: grisumbras  
> Created at: 2021-06-23 13:11:27 UTC  
> Updated at: 2021-06-23 13:13:00 UTC  
> Url: https://github.com/boostorg/json/issues/576#issuecomment-866822706  

I've got these numbers:  
```  
2147483647 == V  
2147483648 == static_cast<float>(V)  
  
2147483647 == static_cast<std::int64_t>(V)  
2147483648 == static_cast<float>(static_cast<std::int64_t>(V))  
  
2147483647 == jv.to_number<float>()  
2147483647 == static_cast<float>(jv.to_number<float>())  
```  
Somehow `to_number` is more precise than `static_cast`.

---

## Comment 3

> Username: grisumbras  
> Created at: 2021-06-23 13:27:26 UTC  
> Url: https://github.com/boostorg/json/issues/576#issuecomment-866835228  

Is there a way I could test a PR on Xcode 7.3 or 8.3?
