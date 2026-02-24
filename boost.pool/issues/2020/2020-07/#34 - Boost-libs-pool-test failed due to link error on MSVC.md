# #34 - Boost\libs\pool\test failed due to link error on MSVC [Open]

> Username: QuellaZhang  
> Created at: 2020-07-31 07:23:32 UTC  
> Updated at: 2022-09-26 16:19:38 UTC  
> Url: https://github.com/boostorg/pool/issues/34  

**Issue description:**  
Found Boost\libs\pool\test https://github.com/boostorg/boost/commit/38d8e0e70e1ea9232914d8a212aed488e32c4b48 link error in a future release of MSVC. Could you please take a look?  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git Boost\src  
2. open a VS 2017 x64 command prompt and browse to Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\pool\test  
  
**ErrorMessage:**  
   Creating library .\pool_msvc_compiler_bug_test.lib and object .\pool_msvc_compiler_bug_test.exp  
pool_msvc_compiler_bug_test.obj : error LNK2019: unresolved external symbol "public: __cdecl boost::archive::codecvt_null<wchar_t>::codecvt_null<wchar_t>(unsigned __int64)" (??0?$codecvt_null@_W@archive@boost@@QEAA@_K@Z) referenced in function "public: void __cdecl boost::archive::codecvt_null<wchar_t>::`default constructor closure'(void)" (??_F?$codecvt_null@_W@archive@boost@@QEAAXXZ)  
.\pool_msvc_compiler_bug_test.exe : fatal error LNK1120: 1 unresolved externals

---

## Comment 1

> Username: michaelpope96  
> Created at: 2020-11-10 23:50:37 UTC  
> Url: https://github.com/boostorg/pool/issues/34#issuecomment-725037146  

I too am having a similar issue when statically linking boost::serialization to my application:   
  
cmake_pch.obj : error LNK2019: unresolved external symbol "public: __cdecl boost::archive::codecvt_null<wchar_t>::codecvt_null<wchar_t>(unsigned __int64)" (??0?$codecvt_null@_W@archive@boost@@QEAA@_K@Z) referenced in function "public: v  
oid __cdecl boost::archive::codecvt_null<wchar_t>::`default constructor closure'(void)" (??_F?$codecvt_null@_W@archive@boost@@QEAAXXZ)  
  
I have tried building Boost 1.74.0 and 1.73.0 with ICU (1.67_1) support on Windows using MSVC2019 and then statically linking to my application. It would seem that sometime after Boost 1.70.0 symbol visibility was tinkered with.

---

## Comment 2

> Username: Jimmy-Hu  
> Created at: 2021-07-29 10:49:44 UTC  
> Url: https://github.com/boostorg/pool/issues/34#issuecomment-889014256  

I have the similar issue as @MichaelAnthonyP96 mentioned.  
  
The environment is:  
  
- OS: Windows 10 Pro version 21H1 build 19043.1110 with Windows Feature Experience Pack 120.2212.3530.0  
  
- IDE: Microsoft Visual Studio 2019 Version 16.10.4  
  
- library version: Boost 1.76.0 statically linking with cmake  
  
The part of adding boost libs in cmake:  
  
```cmake  
# Add boost library  
# Reference: https://github.com/Microsoft/vcpkg/issues/4188  
set(Boost_USE_STATIC_LIBS ON)  
set(Boost_USE_MULTITHREADED OFF)  
set(Boost_USE_STATIC_RUNTIME OFF)  
find_package(BOOST 76.0 REQUIRED COMPONENTS test serialization)  
find_path(BOOST_INCLUDE_DIR boost)  
include_directories(${BOOST_INCLUDE_DIR})  
target_link_libraries(TinyDIP Boost::boost ${BOOST_LIBRARIES})  
```

---

## Comment 3

> Username: UMU618  
> Created at: 2022-09-26 16:19:38 UTC  
> Url: https://github.com/boostorg/pool/issues/34#issuecomment-1258298918  

```  
1>C:\dev\boost_1_80_0\boost\beast\core\detail\type_traits.hpp(67,32): error C4996: 'std::aligned_storage<8,8>': warning STL4034: std::aligned_storage and std::aligned_storage_t are deprecated in C++23. Prefer alignas(T) std::byte t_buff[sizeof(T)]. You can define _SILENCE_CXX23_ALIGNED_STORAGE_DEPRECATION_WARNING or _SILENCE_ALL_CXX23_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
1>C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.33.31629\include\type_traits(1070,1): message : see declaration of 'std::aligned_storage'  
1>C:\dev\boost_1_80_0\boost\beast\core\detail\variant.hpp(32): message : see reference to class template instantiation 'boost::beast::detail::aligned_union<1,const boost::beast::http::detail::chunk_size::value_type *,const boost::asio::const_buffer *,const boost::beast::http::chunk_crlf::value_type *,boost::beast::detail::buffers_cat_view_iterator_base::past_end>' being compiled  
1>C:\dev\boost_1_80_0\boost\beast\core\detail\variant.hpp(32): message : see reference to alias template instantiation 'boost::beast::detail::aligned_union_t<1,const boost::beast::http::detail::chunk_size::value_type*,const boost::asio::const_buffer*,const boost::beast::http::chunk_crlf::value_type*,boost::beast::detail::buffers_cat_view_iterator_base::past_end>' being compiled  
1>C:\dev\boost_1_80_0\boost\beast\core\impl\buffers_cat.hpp(127): message : see reference to class template instantiation 'boost::beast::detail::variant<const boost::beast::http::detail::chunk_size::value_type *,const boost::asio::const_buffer *,const boost::beast::http::chunk_crlf::value_type *,boost::beast::detail::buffers_cat_view_iterator_base::past_end>' being compiled  
1>C:\dev\boost_1_80_0\boost\beast\http\chunk_encode.hpp(251): message : see reference to class template instantiation 'boost::beast::buffers_cat_view<boost::beast::http::detail::chunk_size,boost::asio::const_buffer,boost::beast::http::chunk_crlf>::const_iterator' being compiled  
1>C:\dev\boost_1_80_0\boost\beast\core\detail\type_traits.hpp(67,18): error C4996: 'std::aligned_storage<8,8>::type': warning STL4034: std::aligned_storage and std::aligned_storage_t are deprecated in C++23. Prefer alignas(T) std::byte t_buff[sizeof(T)]. You can define _SILENCE_CXX23_ALIGNED_STORAGE_DEPRECATION_WARNING or _SILENCE_ALL_CXX23_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
1>C:\dev\boost_1_80_0\boost\beast\websocket\detail\decorator.hpp(65,9): error C4996: 'std::aligned_storage<48,8>::type': warning STL4034: std::aligned_storage and std::aligned_storage_t are deprecated in C++23. Prefer alignas(T) std::byte t_buff[sizeof(T)]. You can define _SILENCE_CXX23_ALIGNED_STORAGE_DEPRECATION_WARNING or _SILENCE_ALL_CXX23_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
```
