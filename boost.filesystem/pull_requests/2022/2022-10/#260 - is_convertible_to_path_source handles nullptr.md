# #260 is_convertible_to_path_source handles nullptr [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-10-18 02:58:22 UTC  
> Updated at: 2022-10-18 12:36:10 UTC  
> Closed at: 2022-10-18 12:36:03 UTC  
> Url: https://github.com/boostorg/filesystem/pull/260  

The `is_convertible_to_path_source` should handle `nullptr` properly.  
  
This can cause an error on FreeBSD with clang, where `NULL` is defined as `nullptr`,  which makes `create_directory(parent, nullptr, &local_ec);` call in operations.cpp:3122 invalid.  
  
  
The relevant PRs are https://github.com/boostorg/beast/pull/2543 and https://github.com/boostorg/process/pull/274 .

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2022-10-18 03:02:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/260#issuecomment-1281750942  

Here's the compiler output:  
  
```cpp  
In file included from ../../../libs/filesystem/src/operations.cpp:18:  
In file included from ../../../boost/filesystem/operations.hpp:20:  
In file included from ../../../boost/filesystem/path.hpp:21:  
../../../boost/filesystem/detail/path_traits.hpp:495:16: error: call to '_check_convertible_to_path_source' is ambiguous  
        sizeof(is_convertible_to_path_source< T >::_check_convertible_to_path_source(boost::declval< T const& >())) == sizeof(yes_type);  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../../../boost/type_traits/disjunction.hpp:27:7: note: in instantiation of template class 'boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr_t>' requested here  
    : T { };  
      ^  
../../../boost/type_traits/disjunction.hpp:31:7: note: in instantiation of template class 'boost::disjunction<boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr_t>>' requested here  
    : conditional<bool(T::value), T, disjunction<U...> >::type { };  
      ^  
../../../boost/type_traits/conjunction.hpp:31:24: note: in instantiation of template class 'boost::disjunction<boost::filesystem::detail::path_traits::is_path_source<nullptr_t>, boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr_t>>' requested here  
    : conditional<bool(T::value), conjunction<U...>, T>::type { };  
                       ^  
../../../boost/filesystem/path.hpp:257:20: note: in instantiation of template class 'boost::conjunction<boost::disjunction<boost::filesystem::detail::path_traits::is_path_source<nullptr_t>, boost::filesystem::detail::path_traits::is_convertible_to_path_source<nullptr_t>>, boost::negation<boost::filesystem::detail::path_traits::is_native_path_source<nullptr_t>>>' requested here  
            boost::conjunction<  
                   ^  
../../../boost/filesystem/path.hpp:266:5: note: in instantiation of default argument for 'path<nullptr_t>' required here  
    path(Source const& source)  
    ^~~~~~~~~~~~~~~~~~~~~~~~~~  
../../../libs/filesystem/src/operations.cpp:3122:48: note: while substituting deduced template arguments into function template 'path' [with Source = nullptr_t, $1 = (no value)]  
            created = create_directory(parent, NULL, &local_ec);  
                                               ^  
/usr/include/sys/_null.h:37:14: note: expanded from macro 'NULL'  
#define NULL    nullptr  
                ^  
../../../boost/filesystem/detail/path_traits.hpp:480:21: note: candidate function  
    static yes_type _check_convertible_to_path_source(const char*);  
                    ^  
../../../boost/filesystem/detail/path_traits.hpp:481:21: note: candidate function  
    static yes_type _check_convertible_to_path_source(const wchar_t*);  
                    ^  
```

---

## Comment 2

> Username: Lastique  
> Created_at: 2022-10-18 10:15:23 UTC  
> Url: https://github.com/boostorg/filesystem/pull/260#issuecomment-1282156111  

Creating `path` from `NULL` or `nullptr` is [always an error](http://eel.is/c++draft/fs.path.req#4). If I'm not mistaken, this PR resolves the ambiguity and allows the constructor to compile (by selecting the `path(const value_type* s)` constructor), which is not what I'd prefer. Compilation error is better, although the error you're getting now is rather obscure and could be better.  
  
Re. compilation error in `operations.cpp`, please see if https://github.com/boostorg/filesystem/commit/e3bad3c1c6a1b183a113f2c25652e72af798c0bf fixes it.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2022-10-18 12:36:03 UTC  
> Url: https://github.com/boostorg/filesystem/pull/260#issuecomment-1282316183  

Oh, I thought the trait would SFINAE the ctor, thus disallowing the construction of a path with a nullptr, but not with an ambiguity error.  
  
Locally compiles, checking if the CI jobs work out. Thank you!

---
