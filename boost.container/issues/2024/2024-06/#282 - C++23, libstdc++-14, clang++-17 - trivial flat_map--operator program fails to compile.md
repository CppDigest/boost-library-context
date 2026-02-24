# #282 - C++23, libstdc++-14, clang++-17 : trivial flat_map::operator[] program fails to compile [Closed]

> Username: jcelerier  
> Created at: 2024-06-04 15:51:43 UTC  
> Updated at: 2024-12-05 13:47:14 UTC  
> Closed at: 2024-12-05 13:47:14 UTC  
> Url: https://github.com/boostorg/container/issues/282  

Repro:  
   
```c++  
#include <boost/container/flat_map.hpp>  
  
int main() {  
  boost::container::flat_map<int, int> f;  
  f[0];  
}  
```  
  
The error:   
```  
$ clang++ foo.cpp -std=c++23  
In file included from foo.cpp:1:  
In file included from /usr/include/boost/container/flat_map.hpp:29:  
In file included from /usr/include/boost/container/detail/flat_tree.hpp:29:  
In file included from /usr/include/boost/container/detail/pair.hpp:37:  
In file included from /usr/include/boost/move/adl_move_swap.hpp:35:  
In file included from /usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_algobase.h:64:  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:410:35: error: no matching function for call to 'get'  
  410 |           return _S_convertible<decltype(std::get<0>(std::declval<_UPair>())),  
      |                                          ^~~~~~~~~~~  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:524:22: note: in instantiation of function template specialization 'std::pair<int, int>::_S_convertible_from_pair_like<boost::container::dtl::pair<int, int>>' requested here  
  524 |         constexpr explicit(!_S_convertible_from_pair_like<_UPair>())  
      |                             ^  
/usr/include/boost/container/flat_map.hpp:1652:30: note: while substituting deduced template arguments into function template 'pair' [with _UPair = typename ::boost::move_detail::remove_reference<pair<int, int> &>::type]  
 1652 |          i = this->insert(i, ::boost::move(v));  
      |                              ^  
/usr/include/boost/container/flat_map.hpp:753:85: note: in instantiation of member function 'boost::container::flat_map<int, int>::priv_subscript' requested here  
  753 |       BOOST_MOVE_CONVERSION_AWARE_CATCH( operator[] , key_type, mapped_type&, this->priv_subscript)  
      |                                                                                     ^  
foo.cpp:6:6: note: in instantiation of member function 'boost::container::flat_map<int, int>::operator[]' requested here  
    6 |     f[0];  
      |      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:106:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  106 |     get(pair<_Tp1, _Tp2>& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:110:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  110 |     get(pair<_Tp1, _Tp2>&& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:114:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  114 |     get(const pair<_Tp1, _Tp2>& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:118:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  118 |     get(const pair<_Tp1, _Tp2>&& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:122:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  122 |     get(tuple<_Elements...>& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:126:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  126 |     get(const tuple<_Elements...>& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:130:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  130 |     get(tuple<_Elements...>&& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:134:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  134 |     get(const tuple<_Elements...>&& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:138:5: note: candidate template ignored: could not match 'array' against 'pair'  
  138 |     get(array<_Tp, _Nm>&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:142:5: note: candidate template ignored: could not match 'array' against 'pair'  
  142 |     get(array<_Tp, _Nm>&&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:146:5: note: candidate template ignored: could not match 'array' against 'pair'  
  146 |     get(const array<_Tp, _Nm>&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:150:5: note: candidate template ignored: could not match 'array' against 'pair'  
  150 |     get(const array<_Tp, _Nm>&&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:411:14: error: no matching function for call to 'get'  
  411 |                                 decltype(std::get<1>(std::declval<_UPair>()))>();  
      |                                          ^~~~~~~~~~~  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:106:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  106 |     get(pair<_Tp1, _Tp2>& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:110:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  110 |     get(pair<_Tp1, _Tp2>&& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:114:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  114 |     get(const pair<_Tp1, _Tp2>& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:118:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  118 |     get(const pair<_Tp1, _Tp2>&& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:122:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  122 |     get(tuple<_Elements...>& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:126:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  126 |     get(const tuple<_Elements...>& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:130:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  130 |     get(tuple<_Elements...>&& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:134:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  134 |     get(const tuple<_Elements...>&& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:138:5: note: candidate template ignored: could not match 'array' against 'pair'  
  138 |     get(array<_Tp, _Nm>&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:142:5: note: candidate template ignored: could not match 'array' against 'pair'  
  142 |     get(array<_Tp, _Nm>&&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:146:5: note: candidate template ignored: could not match 'array' against 'pair'  
  146 |     get(const array<_Tp, _Nm>&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:150:5: note: candidate template ignored: could not match 'array' against 'pair'  
  150 |     get(const array<_Tp, _Nm>&&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:410:35: error: no matching function for call to 'get'  
  410 |           return _S_convertible<decltype(std::get<0>(std::declval<_UPair>())),  
      |                                          ^~~~~~~~~~~  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:524:22: note: in instantiation of function template specialization 'std::pair<int, int>::_S_convertible_from_pair_like<boost::container::dtl::pair<int, int> &>' requested here  
  524 |         constexpr explicit(!_S_convertible_from_pair_like<_UPair>())  
      |                             ^  
/usr/include/boost/move/detail/meta_utils.hpp:288:46: note: while substituting deduced template arguments into function template 'pair' [with _UPair = boost::container::dtl::pair<int, int> &]  
  288 |    static const bool value = sizeof(dispatch(trigger())) == sizeof(true_t);  
      |                                              ^  
/usr/include/boost/container/flat_map.hpp:1156:43: note: in instantiation of template class 'boost::move_detail::is_convertible<boost::container::dtl::pair<int, int>, boost::container::dtl::pair<int, int>>' requested here  
 1156 |          , typename dtl::enable_if_c<dtl::is_convertible<Pair BOOST_MOVE_I impl_value_type>::value  
      |                                           ^  
/usr/include/boost/container/flat_map.hpp:1652:20: note: while substituting deduced template arguments into function template 'insert' [with Pair = typename ::boost::move_detail::remove_reference<pair<int, int> &>::type]  
 1652 |          i = this->insert(i, ::boost::move(v));  
      |                    ^  
/usr/include/boost/container/flat_map.hpp:753:85: note: in instantiation of member function 'boost::container::flat_map<int, int>::priv_subscript' requested here  
  753 |       BOOST_MOVE_CONVERSION_AWARE_CATCH( operator[] , key_type, mapped_type&, this->priv_subscript)  
      |                                                                                     ^  
foo.cpp:6:6: note: in instantiation of member function 'boost::container::flat_map<int, int>::operator[]' requested here  
    6 |     f[0];  
      |      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:106:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  106 |     get(pair<_Tp1, _Tp2>& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:110:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  110 |     get(pair<_Tp1, _Tp2>&& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:114:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  114 |     get(const pair<_Tp1, _Tp2>& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:118:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  118 |     get(const pair<_Tp1, _Tp2>&& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:122:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  122 |     get(tuple<_Elements...>& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:126:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  126 |     get(const tuple<_Elements...>& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:130:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  130 |     get(tuple<_Elements...>&& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:134:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  134 |     get(const tuple<_Elements...>&& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:138:5: note: candidate template ignored: could not match 'array' against 'pair'  
  138 |     get(array<_Tp, _Nm>&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:142:5: note: candidate template ignored: could not match 'array' against 'pair'  
  142 |     get(array<_Tp, _Nm>&&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:146:5: note: candidate template ignored: could not match 'array' against 'pair'  
  146 |     get(const array<_Tp, _Nm>&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:150:5: note: candidate template ignored: could not match 'array' against 'pair'  
  150 |     get(const array<_Tp, _Nm>&&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:411:14: error: no matching function for call to 'get'  
  411 |                                 decltype(std::get<1>(std::declval<_UPair>()))>();  
      |                                          ^~~~~~~~~~~  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:106:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  106 |     get(pair<_Tp1, _Tp2>& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:110:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  110 |     get(pair<_Tp1, _Tp2>&& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:114:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  114 |     get(const pair<_Tp1, _Tp2>& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:118:5: note: candidate template ignored: could not match 'std::pair' against 'boost::container::dtl::pair'  
  118 |     get(const pair<_Tp1, _Tp2>&& __in) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:122:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  122 |     get(tuple<_Elements...>& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:126:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  126 |     get(const tuple<_Elements...>& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:130:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  130 |     get(tuple<_Elements...>&& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:134:5: note: candidate template ignored: could not match 'tuple' against 'pair'  
  134 |     get(const tuple<_Elements...>&& __t) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:138:5: note: candidate template ignored: could not match 'array' against 'pair'  
  138 |     get(array<_Tp, _Nm>&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:142:5: note: candidate template ignored: could not match 'array' against 'pair'  
  142 |     get(array<_Tp, _Nm>&&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:146:5: note: candidate template ignored: could not match 'array' against 'pair'  
  146 |     get(const array<_Tp, _Nm>&) noexcept;  
      |     ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/14.1.1/../../../../include/c++/14.1.1/bits/stl_pair.h:150:5: note: candidate template ignored: could not match 'array' against 'pair'  
  150 |     get(const array<_Tp, _Nm>&&) noexcept;  
      |     ^  
4 errors generated.  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-06-05 06:42:51 UTC  
> Url: https://github.com/boostorg/container/issues/282#issuecomment-2149001582  

Looks like a clang/libstdc++ bug as std::get is not found.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-06-24 12:56:38 UTC  
> Url: https://github.com/boostorg/container/issues/282#issuecomment-2186519484  

Thanks for the report. I can't reproduce this issue with clang-17 and clang-18 in Ubuntu 24.04 with current develop version. Can you test it with the latest version and check if the issue is already solved?

---

## Comment 3

> Username: jcelerier  
> Created at: 2024-06-25 12:48:09 UTC  
> Updated at: 2024-06-25 12:49:09 UTC  
> Url: https://github.com/boostorg/container/issues/282#issuecomment-2188848117  

Hm, this happens here in an up-to-date ArchLinux container, assuming boost_1_85_0 extracted in $PWD :  
  
```bash  
$ docker run -v $PWD:/src -it archlinux:latest  
$ pacman -Sy  
$ pacman -S clang   
$ echo '#include <boost/container/flat_map.hpp>  
  
int main() {  
  boost::container::flat_map<int, int> f;  
  f[0];  
}' > foo.cpp  
  
$  clang++ foo.cpp -std=c++23 -I /src/boost_1_85_0  
```

---

## Comment 4

> Username: jcelerier  
> Created at: 2024-06-25 12:50:21 UTC  
> Url: https://github.com/boostorg/container/issues/282#issuecomment-2188852383  

Is there a single-command easy invocation to get current boost develop ? I'm not sure I want to clone every repo...

---

## Comment 5

> Username: igaztanaga  
> Created at: 2024-06-25 21:55:45 UTC  
> Updated at: 2024-06-25 22:01:37 UTC  
> Url: https://github.com/boostorg/container/issues/282#issuecomment-2190037691  

Not an easy way, AFAIK. However Container only depends on Assert, Config, Intrusive and Move. You can automate the download of dependencies of a single library as explained here, in the section "Individual Modules":  
  
https://www.boost.io/doc/user-guide/getting-started.html  
  
Another option is to wait a few weeks until the Boost 1.86 beta tar.gz is generated.

---

## Comment 6

> Username: jcelerier  
> Created at: 2024-12-05 13:47:12 UTC  
> Url: https://github.com/boostorg/container/issues/282#issuecomment-2520374775  

issue is solved in more recent versions of clang so closing
