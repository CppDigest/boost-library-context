# #172 - C++20 consteval error in boost::pfr::names_as_array [Closed]

> Username: QuiteDiam0nd  
> Created at: 2024-06-11 12:08:03 UTC  
> Updated at: 2024-06-11 12:43:18 UTC  
> Closed at: 2024-06-11 12:43:17 UTC  
> Url: https://github.com/boostorg/pfr/issues/172  

### Problem  
Hello!  
I get error with consteval function apply while using `boost::pfr::names_as_array`:  
```  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:112:20:   
error: constexpr variable 'fn' must be initialized by a constant expression  
    constexpr auto fn = skip.apply(sv);  
  
```  
Actually, I found the [same issue in fmt](https://github.com/fmtlib/fmt/issues/2455), but none of the fixes didn't help:  
1. Using `libc++` instead of `libstdc++`.  
2. Setting `set(CMAKE_CXX_STANDARD 17)` instead of `set(CMAKE_CXX_STANDARD 20)`.  
3. Plenty of other CMake tricky fixes.  
  
I'm using `boost 1.85.0`.  
Error occurs on `boost 1.84.0` too.  
Thanks for any help or advice!  
  
### Environment  
I'm using  
```  
clang++ --version  
  
clang version 13.0.1  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
Installed dir: /data2/clang-trunk/bin  
```  
  
```  
add_compile_options("$<$<CONFIG:DEBUG>:-O0>")  
  add_compile_options("$<$<CONFIG:RELEASE>:-O3>")  
  add_compile_options("-Wall")  
  add_compile_options("-Wextra")  
  add_compile_options("-Werror")  
  add_compile_options("-msse4.2")  
  add_compile_options("-Werror=return-type")  
  add_compile_options("-Wno-implicit-fallthrough")  
  add_compile_options("-Wno-unused-parameter")  
  add_compile_options("-pedantic")  
  add_compile_options("-Wno-gnu-label-as-value")  
  add_compile_options("-stdlib=libc++")  
  
  add_link_options("-stdlib=libc++")  
  add_link_options("-fuse-ld=lld")  
  add_link_options("-ldl")  
  
```  
Full text of error:  
  
```  
In file included from /data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name.hpp:23:  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:112:20: error: constexpr variable 'fn' must be initialized by a constant expression  
    constexpr auto fn = skip.apply(sv);  
                   ^    ~~~~~~~~~~~~~~  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:180:20: note: in instantiation of function template specialization 'boost::pfr::detail::name_of_field_impl<..., {&do_not_use_PFR_with_local_types.value.timestamp}>' requested here  
    return detail::name_of_field_impl<MsvcWorkaround, ptr>();  
                   ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:188:54: note: in instantiation of function template specialization 'boost::pfr::detail::name_of_field<..., {&do_not_use_PFR_with_local_types.value.timestamp}>' requested here  
inline constexpr auto stored_name_of_field = detail::name_of_field<T,  
                                                     ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:200:73: note: in instantiation of variable template specialization 'boost::pfr::detail::stored_name_of_field' requested here  
    return detail::sequence_tuple::make_sequence_tuple(std::string_view{stored_name_of_field<T, I>.data()}...);  
                                                                        ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:236:20: note: in instantiation of function template specialization 'boost::pfr::detail::tie_as_names_tuple_impl<myclass::Options, 0UL, 1UL>' requested here  
    return detail::tie_as_names_tuple_impl<T>(detail::make_index_sequence<detail::fields_count<T>()>{});  
                   ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/core_name.hpp:79:17: note: in instantiation of function template specialization 'boost::pfr::detail::tie_as_names_tuple<myclass::Options>' requested here  
        detail::tie_as_names_tuple<T>(),  
                ^  
/...my_project/: note: in instantiation of function template specialization 'boost::pfr::names_as_array<myclass::Options>' requested here  
    auto names = boost::pfr::names_as_array<Options>();  
                             ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:112:30: note: non-constexpr function 'apply' cannot be used in a constant expression  
    constexpr auto fn = skip.apply(sv);  
                             ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:35:32: note: declared here  
    consteval std::string_view apply(std::string_view sv) const noexcept {  
                               ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:114:9: error: static_assert expression is not an integral constant expression  
        !fn.empty(),  
        ^~~~~~~~~~~  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:114:13: note: initializer of 'fn' is not a constant expression  
        !fn.empty(),  
            ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:112:20: note: declared here  
    constexpr auto fn = skip.apply(sv);  
                   ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:120:33: error: non-type template argument is not a constant expression  
    auto res = std::array<char, fn.size()+1>{};  
                                ^~~~~~~~~~~  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:120:36: note: initializer of 'fn' is not a constant expression  
    auto res = std::array<char, fn.size()+1>{};  
                                   ^  
/data2/eipulatov/.conan/data/boost/1.85.0/_/_/package/0458049ba3cdb8489beabfab45dd835a78ef307b/include/boost/pfr/detail/core_name20_static.hpp:112:20: note: declared here  
    constexpr auto fn = skip.apply(sv);  
```

---

## Comment 1

> Username: QuiteDiam0nd  
> Created at: 2024-06-11 12:43:17 UTC  
> Url: https://github.com/boostorg/pfr/issues/172#issuecomment-2160668785  

The problem fixed.   
The issue was with precompiled headers with boost headers.
