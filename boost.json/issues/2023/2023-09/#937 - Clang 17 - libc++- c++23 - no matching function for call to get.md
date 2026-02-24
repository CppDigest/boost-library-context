# #937 - Clang 17 / libc++/ c++23 - no matching function for call to get [Closed]

> Username: jeremiahar  
> Created at: 2023-09-07 08:01:10 UTC  
> Updated at: 2023-10-03 18:49:53 UTC  
> Closed at: 2023-10-03 18:49:52 UTC  
> Url: https://github.com/boostorg/json/issues/937  

Compilation fails on clang 17 rc4 when using libc++ and -std=c++23. It compiles fine with -std=c++20. It also compiles fine with libstdc++.   
  
### Version of Boost  
  
1.83  
  
### Steps necessary to reproduce the problem  
  
```c++  
#include <boost/json/src.hpp>  
```  
  
### All relevant compiler information  
  
Clang 17 rc4 (092b6c5ee3707ea10b9f10d0a674e8d12395369b)  
```  
root@5bbeacbc6171:/# clang++-17 --version  
Debian clang version 17.0.0 (++20230905073144+092b6c5ee370-1~exp1~20230905073243.39)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
### Error information  
  
```  
root@5bbeacbc6171:/# echo -e "#include <boost/json/src.hpp>\n" > main.cpp  
root@5bbeacbc6171:/# clang++-17 -std=c++23 -stdlib=libc++ -I./boost_1_83_0/ main.cpp -o json_test  
In file included from main.cpp:1:  
In file included from ./boost_1_83_0/boost/json/src.hpp:27:  
In file included from ./boost_1_83_0/boost/json.hpp:13:  
In file included from ./boost_1_83_0/boost/json/detail/config.hpp:15:  
In file included from ./boost_1_83_0/boost/throw_exception.hpp:21:  
In file included from ./boost_1_83_0/boost/exception/exception.hpp:9:  
In file included from ./boost_1_83_0/boost/assert/source_location.hpp:15:  
In file included from /usr/lib/llvm-17/bin/../include/c++/v1/string:574:  
In file included from /usr/lib/llvm-17/bin/../include/c++/v1/__functional/hash.h:24:  
/usr/lib/llvm-17/bin/../include/c++/v1/__utility/pair.h:294:47: error: no matching function for call to 'get'  
  294 |             return !is_convertible_v<decltype(std::get<0>(std::declval<_PairLike&&>())), first_type> ||  
      |                                               ^~~~~~~~~~~  
/usr/lib/llvm-17/bin/../include/c++/v1/__utility/pair.h:303:46: note: in instantiation of function template specialization 'std::pair<boost::core::basic_string_view<char>, boost::json::value>::__pair_like_explicit_wknd<const boost::json::key_value_pair &, true>' requested here  
  303 |     _LIBCPP_HIDE_FROM_ABI constexpr explicit(__pair_like_explicit_wknd<_PairLike>())  
      |                                              ^  
./boost_1_83_0/boost/json/impl/object.ipp:349:32: note: while substituting deduced template arguments into function template 'pair' [with _PairLike = const value_type &]  
  349 |                 key_value_pair(v, sp_);  
      |                                ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:32:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'const boost::json::key_value_pair'  
   32 | get(tuple<_Tp...>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:37:1: note: candidate template ignored: could not match 'const tuple<_Tp...>' against 'const boost::json::key_value_pair'  
   37 | get(const tuple<_Tp...>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:42:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'const boost::json::key_value_pair'  
   42 | get(tuple<_Tp...>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:47:1: note: candidate template ignored: could not match 'const tuple<_Tp...>' against 'const boost::json::key_value_pair'  
   47 | get(const tuple<_Tp...>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:54:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'const boost::json::key_value_pair'  
   54 | get(pair<_T1, _T2>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:59:1: note: candidate template ignored: could not match 'const pair<_T1, _T2>' against 'const boost::json::key_value_pair'  
   59 | get(const pair<_T1, _T2>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:65:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'const boost::json::key_value_pair'  
   65 | get(pair<_T1, _T2>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:70:1: note: candidate template ignored: could not match 'const pair<_T1, _T2>' against 'const boost::json::key_value_pair'  
   70 | get(const pair<_T1, _T2>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:76:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'const boost::json::key_value_pair'  
   76 | get(array<_Tp, _Size>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:81:1: note: candidate template ignored: could not match 'const array<_Tp, _Size>' against 'const boost::json::key_value_pair'  
   81 | get(const array<_Tp, _Size>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:87:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'const boost::json::key_value_pair'  
   87 | get(array<_Tp, _Size>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:92:1: note: candidate template ignored: could not match 'const array<_Tp, _Size>' against 'const boost::json::key_value_pair'  
   92 | get(const array<_Tp, _Size>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:101:38: note: candidate template ignored: could not match 'const subrange<_Iter, _Sent, _Kind>' against 'const boost::json::key_value_pair'  
  101 | _LIBCPP_HIDE_FROM_ABI constexpr auto get(const subrange<_Iter, _Sent, _Kind>& __subrange);  
      |                                      ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:105:38: note: candidate template ignored: could not match 'subrange<_Iter, _Sent, _Kind>' against 'const boost::json::key_value_pair'  
  105 | _LIBCPP_HIDE_FROM_ABI constexpr auto get(subrange<_Iter, _Sent, _Kind>&& __subrange);  
      |                                      ^  
In file included from main.cpp:1:  
In file included from ./boost_1_83_0/boost/json/src.hpp:27:  
In file included from ./boost_1_83_0/boost/json.hpp:13:  
In file included from ./boost_1_83_0/boost/json/detail/config.hpp:15:  
In file included from ./boost_1_83_0/boost/throw_exception.hpp:21:  
In file included from ./boost_1_83_0/boost/exception/exception.hpp:9:  
In file included from ./boost_1_83_0/boost/assert/source_location.hpp:15:  
In file included from /usr/lib/llvm-17/bin/../include/c++/v1/string:574:  
In file included from /usr/lib/llvm-17/bin/../include/c++/v1/__functional/hash.h:24:  
/usr/lib/llvm-17/bin/../include/c++/v1/__utility/pair.h:295:47: error: no matching function for call to 'get'  
  295 |                    !is_convertible_v<decltype(std::get<1>(std::declval<_PairLike&&>())), second_type>;  
      |                                               ^~~~~~~~~~~  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:32:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'const boost::json::key_value_pair'  
   32 | get(tuple<_Tp...>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:37:1: note: candidate template ignored: could not match 'const tuple<_Tp...>' against 'const boost::json::key_value_pair'  
   37 | get(const tuple<_Tp...>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:42:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'const boost::json::key_value_pair'  
   42 | get(tuple<_Tp...>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:47:1: note: candidate template ignored: could not match 'const tuple<_Tp...>' against 'const boost::json::key_value_pair'  
   47 | get(const tuple<_Tp...>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:54:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'const boost::json::key_value_pair'  
   54 | get(pair<_T1, _T2>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:59:1: note: candidate template ignored: could not match 'const pair<_T1, _T2>' against 'const boost::json::key_value_pair'  
   59 | get(const pair<_T1, _T2>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:65:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'const boost::json::key_value_pair'  
   65 | get(pair<_T1, _T2>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:70:1: note: candidate template ignored: could not match 'const pair<_T1, _T2>' against 'const boost::json::key_value_pair'  
   70 | get(const pair<_T1, _T2>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:76:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'const boost::json::key_value_pair'  
   76 | get(array<_Tp, _Size>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:81:1: note: candidate template ignored: could not match 'const array<_Tp, _Size>' against 'const boost::json::key_value_pair'  
   81 | get(const array<_Tp, _Size>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:87:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'const boost::json::key_value_pair'  
   87 | get(array<_Tp, _Size>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:92:1: note: candidate template ignored: could not match 'const array<_Tp, _Size>' against 'const boost::json::key_value_pair'  
   92 | get(const array<_Tp, _Size>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:101:38: note: candidate template ignored: could not match 'const subrange<_Iter, _Sent, _Kind>' against 'const boost::json::key_value_pair'  
  101 | _LIBCPP_HIDE_FROM_ABI constexpr auto get(const subrange<_Iter, _Sent, _Kind>& __subrange);  
      |                                      ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:105:38: note: candidate template ignored: could not match 'subrange<_Iter, _Sent, _Kind>' against 'const boost::json::key_value_pair'  
  105 | _LIBCPP_HIDE_FROM_ABI constexpr auto get(subrange<_Iter, _Sent, _Kind>&& __subrange);  
      |                                      ^  
In file included from main.cpp:1:  
In file included from ./boost_1_83_0/boost/json/src.hpp:27:  
In file included from ./boost_1_83_0/boost/json.hpp:13:  
In file included from ./boost_1_83_0/boost/json/detail/config.hpp:15:  
In file included from ./boost_1_83_0/boost/throw_exception.hpp:21:  
In file included from ./boost_1_83_0/boost/exception/exception.hpp:9:  
In file included from ./boost_1_83_0/boost/assert/source_location.hpp:15:  
In file included from /usr/lib/llvm-17/bin/../include/c++/v1/string:574:  
In file included from /usr/lib/llvm-17/bin/../include/c++/v1/__functional/hash.h:24:  
/usr/lib/llvm-17/bin/../include/c++/v1/__utility/pair.h:294:47: error: no matching function for call to 'get'  
  294 |             return !is_convertible_v<decltype(std::get<0>(std::declval<_PairLike&&>())), first_type> ||  
      |                                               ^~~~~~~~~~~  
/usr/lib/llvm-17/bin/../include/c++/v1/__utility/pair.h:303:46: note: in instantiation of function template specialization 'std::pair<boost::core::basic_string_view<char>, boost::json::value>::__pair_like_explicit_wknd<boost::json::key_value_pair, true>' requested here  
  303 |     _LIBCPP_HIDE_FROM_ABI constexpr explicit(__pair_like_explicit_wknd<_PairLike>())  
      |                                              ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__type_traits/is_nothrow_move_constructible.h:28:70: note: while substituting deduced template arguments into function template 'pair' [with _PairLike = boost::json::key_value_pair]  
   28 |     : public integral_constant<bool, __is_nothrow_constructible(_Tp, __add_rvalue_reference_t<_Tp>)> {};  
      |                                                                      ^  
./boost_1_83_0/boost/json/pilfer.hpp:121:14: note: in instantiation of template class 'std::is_nothrow_move_constructible<boost::json::key_value_pair>' requested here  
  121 |         std::is_nothrow_move_constructible<T>::value ||  
      |              ^  
./boost_1_83_0/boost/json/pilfer.hpp:191:9: note: in instantiation of template class 'boost::json::is_pilfer_constructible<boost::json::key_value_pair>' requested here  
  191 |         is_pilfer_constructible<U>::value, "");  
      |         ^  
./boost_1_83_0/boost/json/impl/object.hpp:559:26: note: in instantiation of function template specialization 'boost::json::pilfer<boost::json::key_value_pair &>' requested here  
  559 |     return { insert_impl(pilfer(kv), search_result.second), true };  
      |                          ^  
./boost_1_83_0/boost/json/impl/object.hpp:432:12: note: in instantiation of function template specialization 'boost::json::object::emplace_impl<boost::core::basic_string_view<char> &, std::nullptr_t>' requested here  
  432 |     return emplace_impl( key, key, static_cast<Arg&&>(arg) );  
      |            ^  
./boost_1_83_0/boost/json/impl/object.ipp:604:9: note: in instantiation of function template specialization 'boost::json::object::emplace<std::nullptr_t>' requested here  
  604 |         emplace(key, nullptr);  
      |         ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:32:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'boost::json::key_value_pair'  
   32 | get(tuple<_Tp...>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:37:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'boost::json::key_value_pair'  
   37 | get(const tuple<_Tp...>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:42:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'boost::json::key_value_pair'  
   42 | get(tuple<_Tp...>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:47:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'boost::json::key_value_pair'  
   47 | get(const tuple<_Tp...>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:54:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'boost::json::key_value_pair'  
   54 | get(pair<_T1, _T2>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:59:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'boost::json::key_value_pair'  
   59 | get(const pair<_T1, _T2>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:65:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'boost::json::key_value_pair'  
   65 | get(pair<_T1, _T2>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:70:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'boost::json::key_value_pair'  
   70 | get(const pair<_T1, _T2>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:76:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'boost::json::key_value_pair'  
   76 | get(array<_Tp, _Size>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:81:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'boost::json::key_value_pair'  
   81 | get(const array<_Tp, _Size>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:87:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'boost::json::key_value_pair'  
   87 | get(array<_Tp, _Size>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:92:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'boost::json::key_value_pair'  
   92 | get(const array<_Tp, _Size>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:101:38: note: candidate template ignored: could not match 'subrange<_Iter, _Sent, _Kind>' against 'boost::json::key_value_pair'  
  101 | _LIBCPP_HIDE_FROM_ABI constexpr auto get(const subrange<_Iter, _Sent, _Kind>& __subrange);  
      |                                      ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:105:38: note: candidate template ignored: could not match 'subrange<_Iter, _Sent, _Kind>' against 'boost::json::key_value_pair'  
  105 | _LIBCPP_HIDE_FROM_ABI constexpr auto get(subrange<_Iter, _Sent, _Kind>&& __subrange);  
      |                                      ^  
In file included from main.cpp:1:  
In file included from ./boost_1_83_0/boost/json/src.hpp:27:  
In file included from ./boost_1_83_0/boost/json.hpp:13:  
In file included from ./boost_1_83_0/boost/json/detail/config.hpp:15:  
In file included from ./boost_1_83_0/boost/throw_exception.hpp:21:  
In file included from ./boost_1_83_0/boost/exception/exception.hpp:9:  
In file included from ./boost_1_83_0/boost/assert/source_location.hpp:15:  
In file included from /usr/lib/llvm-17/bin/../include/c++/v1/string:574:  
In file included from /usr/lib/llvm-17/bin/../include/c++/v1/__functional/hash.h:24:  
/usr/lib/llvm-17/bin/../include/c++/v1/__utility/pair.h:295:47: error: no matching function for call to 'get'  
  295 |                    !is_convertible_v<decltype(std::get<1>(std::declval<_PairLike&&>())), second_type>;  
      |                                               ^~~~~~~~~~~  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:32:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'boost::json::key_value_pair'  
   32 | get(tuple<_Tp...>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:37:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'boost::json::key_value_pair'  
   37 | get(const tuple<_Tp...>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:42:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'boost::json::key_value_pair'  
   42 | get(tuple<_Tp...>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:47:1: note: candidate template ignored: could not match 'tuple<_Tp...>' against 'boost::json::key_value_pair'  
   47 | get(const tuple<_Tp...>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:54:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'boost::json::key_value_pair'  
   54 | get(pair<_T1, _T2>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:59:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'boost::json::key_value_pair'  
   59 | get(const pair<_T1, _T2>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:65:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'boost::json::key_value_pair'  
   65 | get(pair<_T1, _T2>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:70:1: note: candidate template ignored: could not match 'pair<_T1, _T2>' against 'boost::json::key_value_pair'  
   70 | get(const pair<_T1, _T2>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:76:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'boost::json::key_value_pair'  
   76 | get(array<_Tp, _Size>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:81:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'boost::json::key_value_pair'  
   81 | get(const array<_Tp, _Size>&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:87:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'boost::json::key_value_pair'  
   87 | get(array<_Tp, _Size>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:92:1: note: candidate template ignored: could not match 'array<_Tp, _Size>' against 'boost::json::key_value_pair'  
   92 | get(const array<_Tp, _Size>&&) _NOEXCEPT;  
      | ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:101:38: note: candidate template ignored: could not match 'subrange<_Iter, _Sent, _Kind>' against 'boost::json::key_value_pair'  
  101 | _LIBCPP_HIDE_FROM_ABI constexpr auto get(const subrange<_Iter, _Sent, _Kind>& __subrange);  
      |                                      ^  
/usr/lib/llvm-17/bin/../include/c++/v1/__fwd/get.h:105:38: note: candidate template ignored: could not match 'subrange<_Iter, _Sent, _Kind>' against 'boost::json::key_value_pair'  
  105 | _LIBCPP_HIDE_FROM_ABI constexpr auto get(subrange<_Iter, _Sent, _Kind>&& __subrange);  
      |                                      ^  
  
```  
  
Libcxx sources:  
https://github.com/llvm/llvm-project/blob/llvmorg-17.0.0-rc4/libcxx/include/  
  
### Reproduction in Debian 12 docker image  
  
```bash  
docker run -it debian:12  
  
  
apt-get update  
apt-get install -y wget lsb-release gpg software-properties-common  
wget https://apt.llvm.org/llvm.sh  
chmod +x llvm.sh  
./llvm.sh 17 all  
# Run the script again after pressing enter because it fails to find the packages the first time  
./llvm.sh 17 all  
  
wget https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.tar.gz  
tar -xf boost_1_83_0.tar.gz  
  
echo -e "#include <boost/json/src.hpp>\n" > main.cpp  
clang++-17 -std=c++23 -stdlib=libc++ -I./boost_1_83_0/ main.cpp -o json_test  
  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-09-07 15:20:57 UTC  
> Url: https://github.com/boostorg/json/issues/937#issuecomment-1710344911  

This appears to be a libc++ bug.  
I filed an issue (https://github.com/llvm/llvm-project/issues/65620).

---

## Comment 2

> Username: jeremiahar  
> Created at: 2023-10-03 18:49:52 UTC  
> Url: https://github.com/boostorg/json/issues/937#issuecomment-1745536137  

It's fixed in clang 17.0.2 https://github.com/llvm/llvm-project/commit/23988a1d82d51670e100791b8a8745e5d8457d35
