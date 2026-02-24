# #270 - Compile error seems to be coming from filesystem/directory.cpp [Closed]

> Username: vinniefalco  
> Created at: 2022-12-17 05:09:40 UTC  
> Updated at: 2022-12-17 21:58:12 UTC  
> Closed at: 2022-12-17 20:47:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/270  

```  
gcc.compile.c++ bin.v2/libs/filesystem/build/gcc-12/release/cxxstd-20-iso/threading-multi/visibility-hidden/directory.o  
In file included from /usr/include/c++/12/string:40,  
                 from ./boost/assert/source_location.hpp:15,  
                 from ./boost/exception/exception.hpp:9,  
                 from ./boost/throw_exception.hpp:21,  
                 from libs/filesystem/src/directory.cpp:16:  
In static member function 'static constexpr std::char_traits<char>::char_type* std::char_traits<char>::copy(char_type*, const char_type*, std::size_t)',  
    inlined from 'static constexpr void std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::_S_copy(_CharT*, const _CharT*, size_type) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]' at /usr/include/c++/12/bits/basic_string.h:423:21,  
    inlined from 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Allocator>& std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::_M_replace(size_type, size_type, const _CharT*, size_type) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]' at /usr/include/c++/12/bits/basic_string.tcc:532:22,  
    inlined from 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>& std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::replace(size_type, size_type, const _CharT*, size_type) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]' at /usr/include/c++/12/bits/basic_string.h:2171:19,  
    inlined from 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>& std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::replace(__const_iterator, __const_iterator, const _CharT*, const _CharT*) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]' at /usr/include/c++/12/bits/basic_string.h:2380:22,  
    inlined from 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>& std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::assign(_InputIterator, _InputIterator) [with _InputIterator = const char*; <template-parameter-2-2> = void; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]' at /usr/include/c++/12/bits/basic_string.h:1682:31,  
    inlined from 'boost::filesystem::path::assign_op::result_type boost::filesystem::path::assign_op::operator()(const boost::filesystem::path::value_type*, const boost::filesystem::path::value_type*, const boost::filesystem::path::codecvt_type*) const' at ./boost/filesystem/path.hpp:171:37,  
    inlined from 'void boost::filesystem::detail::path_traits::dispatch(const char*, Callback, const codecvt_type*, ntcts_type_tag) [with Callback = boost::filesystem::path::assign_op]' at ./boost/filesystem/detail/path_traits.hpp:406:7,  
    inlined from 'void boost::filesystem::detail::path_traits::dispatch(const Source&, Callback, const codecvt_type*) [with Source = char [2]; Callback = boost::filesystem::path::assign_op]' at ./boost/filesystem/detail/path_traits.hpp:463:26,  
    inlined from 'typename boost::enable_if_c<boost::filesystem::detail::path_traits::is_path_source<typename boost::remove_cv<T>::type>::value, boost::filesystem::path&>::type boost::filesystem::path::assign(const Source&) [with Source = char [2]]' at ./boost/filesystem/path.hpp:461:38,  
    inlined from 'boost::system::error_code boost::filesystem::detail::{anonymous}::dir_itr_create(boost::intrusive_ptr<boost::filesystem::detail::dir_itr_imp>&, const boost::filesystem::path&, unsigned int, boost::filesystem::detail::directory_iterator_params*, boost::filesystem::path&, boost::filesystem::file_status&, boost::filesystem::file_status&)' at libs/filesystem/src/directory.cpp:420:26,  
    inlined from 'void boost::filesystem::detail::directory_iterator_construct(boost::filesystem::directory_iterator&, const boost::filesystem::path&, unsigned int, directory_iterator_params*, boost::system::error_code*)' at libs/filesystem/src/directory.cpp:1086:51:  
/usr/include/c++/12/bits/char_traits.h:431:56: error: 'void* __builtin_memcpy(void*, const void*, long unsigned int)' accessing 9223372036854775810 or more bytes at offsets -4611686018427387902 and [-4611686018427387903, 4611686018427387904] may overlap up to 9223372036854775813 bytes at offset -3 [-Werror=restrict]  
  431 |         return static_cast<char_type*>(__builtin_memcpy(__s1, __s2, __n));  
      |                                        ~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~  
cc1plus: all warnings being treated as errors  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2022-12-17 20:47:04 UTC  
> Updated at: 2022-12-17 20:48:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/270#issuecomment-1356461860  

This is a gcc 12 [bug](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=105651).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-12-17 21:49:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/270#issuecomment-1356478729  

Do we have a way to silence it using pragma or ifdef?

---

## Comment 3

> Username: Lastique  
> Created at: 2022-12-17 21:58:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/270#issuecomment-1356479855  

The warning comes from libstdc++, `<string>` to be precise, and it may get triggered by any assignment or appending operation. So basically one would have to wrap everything with pragmas. You're better off adding `-Wno-restrict` in the command line.
