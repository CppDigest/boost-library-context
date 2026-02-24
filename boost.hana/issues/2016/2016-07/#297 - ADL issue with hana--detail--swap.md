# #297 - ADL issue with hana::detail::swap [Closed]

> Username: ldionne  
> Created at: 2016-07-29 15:41:41 UTC  
> Updated at: 2016-08-02 03:06:51 UTC  
> Closed at: 2016-08-02 03:06:51 UTC  
> Url: https://github.com/boostorg/hana/issues/297  

The following code fails on both Clang and GCC:  
  
``` c++  
// Copyright Louis Dionne 2013-2016  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)  
  
#include <boost/hana/integral_constant.hpp>  
#include <boost/hana/map.hpp>  
#include <boost/hana/pair.hpp>  
  
#include <memory>  
namespace hana = boost::hana;  
  
  
int main() {  
    auto map = hana::make_map(  
        hana::make_pair(hana::int_c<0>, 123),  
        hana::make_pair(hana::int_c<1>, 456)  
    );  
  
    std::unique_ptr<decltype(map)> p1{}, p2{};  
    using std::swap;  
    swap(p1, p2);  
}  
```  
  
The error (on Clang) is  
  
```  
In file included from /Users/ldionne/code/hana/test/issues/github_next.cpp:5:  
In file included from /Users/ldionne/code/hana/include/boost/hana/integral_constant.hpp:13:  
In file included from /Users/ldionne/code/hana/include/boost/hana/bool.hpp:13:  
In file included from /Users/ldionne/code/hana/include/boost/hana/fwd/bool.hpp:13:  
In file included from /Users/ldionne/code/hana/include/boost/hana/fwd/integral_constant.hpp:17:  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/type_traits:3624:22: error:  
      call to 'swap' is ambiguous  
    typedef decltype(swap(_VSTD::declval<_Tp&>(), _VSTD::declval<_Tp&>())) type;  
                     ^~~~  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/type_traits:3632:48: note:  
      in instantiation of template class  
      'std::__1::__detail::__swappable<boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<boost::hana::integral_constant<long  
      long, 0>, 0>, boost::hana::detail::bucket<boost::hana::integral_constant<long long, 1>, 1> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>, int>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, int> > > *>' requested here  
    : public integral_constant<bool, __detail::__swappable<_Tp>::value>  
                                               ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/type_traits:3653:41: note:  
      in instantiation of template class  
      'std::__1::__is_swappable<boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<boost::hana::integral_constant<long  
      long, 0>, 0>, boost::hana::detail::bucket<boost::hana::integral_constant<long long, 1>, 1> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>, int>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, int> > > *>' requested here  
    : public __is_nothrow_swappable_imp<__is_swappable<_Tp>::value, _Tp>  
                                        ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/memory:2462:20: note:  
      in instantiation of template class  
      'std::__1::__is_nothrow_swappable<boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<boost::hana::integral_constant<long  
      long, 0>, 0>, boost::hana::detail::bucket<boost::hana::integral_constant<long long, 1>, 1> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>, int>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, int> > > *>' requested here  
        _NOEXCEPT_(__is_nothrow_swappable<_T1>::value &&  
                   ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/memory:2719:17: note:  
      in instantiation of exception specification for 'swap' requested here  
        {__ptr_.swap(__u.__ptr_);}  
                ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/memory:2959:75: note:  
      in instantiation of member function  
      'std::__1::unique_ptr<boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<boost::hana::integral_constant<long  
      long, 0>, 0>, boost::hana::detail::bucket<boost::hana::integral_constant<long long, 1>, 1> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>, int>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, int> > >,  
      std::__1::default_delete<boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<boost::hana::integral_constant<long  
      long, 0>, 0>, boost::hana::detail::bucket<boost::hana::integral_constant<long long, 1>, 1> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>, int>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, int> > > > >::swap' requested here  
swap(unique_ptr<_Tp, _Dp>& __x, unique_ptr<_Tp, _Dp>& __y) _NOEXCEPT {__x.swap(__y);}  
                                                                          ^  
/Users/ldionne/code/hana/test/issues/github_next.cpp:21:5: note: in instantiation of function template specialization  
      'std::__1::swap<boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<boost::hana::integral_constant<long  
      long, 0>, 0>, boost::hana::detail::bucket<boost::hana::integral_constant<long long, 1>, 1> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>, int>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, int> > >,  
      std::__1::default_delete<boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<boost::hana::integral_constant<long  
      long, 0>, 0>, boost::hana::detail::bucket<boost::hana::integral_constant<long long, 1>, 1> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>, int>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, int> > > > >' requested here  
    swap(p1, p2);  
    ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/type_traits:3594:1: note:  
      candidate function [with _Tp =  
      boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<boost::hana::integral_constant<long  
      long, 0>, 0>, boost::hana::detail::bucket<boost::hana::integral_constant<long long, 1>, 1> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>, int>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, int> > > *]  
swap(_Tp& __x, _Tp& __y) _NOEXCEPT_(is_nothrow_move_constructible<_Tp>::value &&  
^  
/Users/ldionne/code/hana/include/boost/hana/detail/algorithm.hpp:23:20: note: candidate function [with T =  
      boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<boost::hana::integral_constant<long  
      long, 0>, 0>, boost::hana::detail::bucket<boost::hana::integral_constant<long long, 1>, 1> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>, int>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, int> > > *]  
    constexpr void swap(T& x, T& y) {  
                   ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/type_traits:3619:7: note:  
      candidate function  
__nat swap(__any, __any);  
      ^  
```  
  
The issue appears to be that the `detail` namespace defines `swap`, and the type of `hana::make_map(...)` is something like `hana::map<hana::detail::hash_table<...>>`. Hence, `hana::detail` is an associated namespace of the object, and `hana::detail::swap` (which doesn't constrain on its parameters at all) gets picked up. A potential fix would be to simply rename `detail::swap` to something else, since it is only used internally anyway.
