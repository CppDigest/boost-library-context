# #519 - std::aligned_storage and std::aligned_union deprecated in C++23 [Closed]

> Username: ecatmur  
> Created at: 2023-08-01 22:15:36 UTC  
> Updated at: 2025-07-03 21:00:03 UTC  
> Closed at: 2025-07-03 21:00:03 UTC  
> Url: https://github.com/boostorg/hana/issues/519  

https://godbolt.org/z/Yx16avdqP  
```c++  
/opt/compiler-explorer/libs/boost_1_82_0/boost/hana/traits.hpp: In member function 'constexpr auto boost::hana::traits::aligned_storage_t::operator()(const Len&, const Align&) const':  
/opt/compiler-explorer/libs/boost_1_82_0/boost/hana/traits.hpp:173:42: warning: 'template<long unsigned int _Len, long unsigned int _Align> struct std::aligned_storage' is deprecated [-Wdeprecated-declarations]  
  173 |             using Result = typename std::aligned_storage<len, align>::type;  
      |                                          ^~~~~~~~~~~~~~~  
In file included from /opt/compiler-explorer/libs/boost_1_82_0/boost/hana/core/default.hpp:17,  
                 from /opt/compiler-explorer/libs/boost_1_82_0/boost/hana/concept/struct.hpp:17,  
                 from /opt/compiler-explorer/libs/boost_1_82_0/boost/hana/accessors.hpp:15,  
                 from /opt/compiler-explorer/libs/boost_1_82_0/boost/hana.hpp:58:  
/opt/compiler-explorer/gcc-trunk-20230801/include/c++/14.0.0/type_traits:2099:5: note: declared here  
 2099 |     aligned_storage  
      |     ^~~~~~~~~~~~~~~  
/opt/compiler-explorer/libs/boost_1_82_0/boost/hana/traits.hpp: In member function 'constexpr auto boost::hana::traits::aligned_storage_t::operator()(const Len&) const':  
/opt/compiler-explorer/libs/boost_1_82_0/boost/hana/traits.hpp:180:42: warning: 'template<long unsigned int _Len, long unsigned int _Align> struct std::aligned_storage' is deprecated [-Wdeprecated-declarations]  
  180 |             using Result = typename std::aligned_storage<len>::type;  
      |                                          ^~~~~~~~~~~~~~~  
/opt/compiler-explorer/gcc-trunk-20230801/include/c++/14.0.0/type_traits:2099:5: note: declared here  
 2099 |     aligned_storage  
      |     ^~~~~~~~~~~~~~~  
/opt/compiler-explorer/libs/boost_1_82_0/boost/hana/traits.hpp: In member function 'constexpr auto boost::hana::traits::aligned_union_t::operator()(const Len&, const T& ...) const':  
/opt/compiler-explorer/libs/boost_1_82_0/boost/hana/traits.hpp:189:42: warning: 'template<long unsigned int _Len, class ... _Types> struct std::aligned_union' is deprecated [-Wdeprecated-declarations]  
  189 |             using Result = typename std::aligned_union<len, typename T::type...>::type;  
      |                                          ^~~~~~~~~~~~~  
/opt/compiler-explorer/gcc-trunk-20230801/include/c++/14.0.0/type_traits:2144:5: note: declared here  
 2144 |     aligned_union  
      |     ^~~~~~~~~~~~~  
```
