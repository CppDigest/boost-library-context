# #1229 - Re-architect `<charconv>` to work better with string construction [Closed]

> Username: mborland  
> Created at: 2025-11-11 08:14:39 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-12 13:04:49 UTC  
> Url: https://github.com/boostorg/decimal/issues/1229  

In our promotion example we get the following from Clang 6-14 only:  
  
```  
bin.v2/libs/decimal/test/promotion.test/clang-linux-6/debug/x86_64/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/promotion.o: In function `decimal64_t':  
/__w/decimal/boost-root/./boost/decimal/decimal64_t.hpp:2284: undefined reference to `boost::decimal::from_chars_result boost::decimal::from_chars<boost::decimal::decimal64_t>(char const*, char const*, boost::decimal::decimal64_t&, boost::decimal::chars_format)'  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++-6.0"    -o "bin.v2/libs/decimal/test/promotion.test/clang-linux-6/debug/x86_64/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/promotion" -Wl,--start-group "bin.v2/libs/decimal/test/promotion.test/clang-linux-6/debug/x86_64/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/promotion.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -pthread -std=c++14 -g -fvisibility=hidden -fvisibility-inlines-hidden -m64 --target=x86_64-pc-linux   
  
...failed clang-linux.link bin.v2/libs/decimal/test/promotion.test/clang-linux-6/debug/x86_64/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/promotion...  
```  
  
We can probably move the guts of charconv into implementation files and then use `from_chars_impl` from the constructor rather than the full-fat charconv. Then we avoid pulling in all of charconv just for the types as well
