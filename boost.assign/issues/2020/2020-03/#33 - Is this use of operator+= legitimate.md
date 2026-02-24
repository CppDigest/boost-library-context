# #33 - Is this use of operator+= legitimate? [Closed]

> Username: giomasce  
> Created at: 2020-03-22 09:07:11 UTC  
> Updated at: 2024-12-28 17:26:38 UTC  
> Closed at: 2024-12-28 17:26:37 UTC  
> Url: https://github.com/boostorg/assign/issues/33  

I am Debian's maintainer for Boost packages.  
  
In https://gitlab.com/hepcedar/rivet/-/blob/master/src/Tools/RivetPaths.cc#L58 Boost.Assign's `operator+=` is used on `std::vector<std::string>` and `std::vector<std::string>`. It used to compile with Boost 1.67, but it doesn't any more with Boost 1.71. The error is  
  
```  
In file included from /usr/include/boost/assign/std/vector.hpp:18,  
                 from /usr/include/boost/assign/std.hpp:18,  
                 from /usr/include/boost/assign.hpp:19,  
                 from ../../include/Rivet/RivetBoost.hh:6,  
                 from ../../include/Rivet/Math/MathUtils.hh:6,  
                 from ../../include/Rivet/Rivet.hh:42,  
                 from RivetPaths.cc:1:  
/usr/include/boost/assign/list_inserter.hpp: In instantiation of 'void boost::assign_detail::call_push_back<C>::operator()(T&&) [with T = std::vector<std::__cxx11::basic_string<char> >; C = std::vector<std::__cxx11::basic_string<char> >]':  
/usr/include/boost/assign/list_inserter.hpp:414:13:   required from 'void boost::assign::list_inserter<Function, Argument>::insert(boost::assign::list_inserter<Function, Argument>::single_arg_type, T&&) [with T = std::vector<std::__cxx11::basic_string<char> >; Function = boost::assign_detail::call_push_back<std::vector<std::__cxx11::basic_string<char> > >; Argument = std::__cxx11::basic_string<char>]'  
/usr/include/boost/assign/list_inserter.hpp:406:13:   required from 'boost::assign::list_inserter<Function, Argument>& boost::assign::list_inserter<Function, Argument>::operator()(Ts&& ...) [with Ts = {std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >}; Function = boost::assign_detail::call_push_back<std::vector<std::__cxx11::basic_string<char> > >; Argument = std::__cxx11::basic_string<char>]'  
/usr/include/boost/assign/std/vector.hpp:42:30:   required from 'boost::assign::list_inserter<boost::assign_detail::call_push_back<std::vector<_Tp, _Alloc> >, V> boost::assign::operator+=(std::vector<_Tp, _Alloc>&, V2&&) [with V = std::__cxx11::basic_string<char>; A = std::allocator<std::__cxx11::basic_string<char> >; V2 = std::vector<std::__cxx11::basic_string<char> >]'  
RivetPaths.cc:57:28:   required from here  
/usr/include/boost/assign/list_inserter.hpp:91:13: error: no matching function for call to 'std::vector<std::__cxx11::basic_string<char> >::push_back(std::vector<std::__cxx11::basic_string<char> >)'  
   91 |             c_.push_back(boost::forward<T>(r));  
      |             ^~  
In file included from /usr/include/c++/9/vector:67,  
                 from ../../include/Rivet/RivetSTL.hh:11,  
                 from ../../include/Rivet/Rivet.hh:8,  
                 from RivetPaths.cc:1:  
/usr/include/c++/9/bits/stl_vector.h:1184:7: note: candidate: 'void std::vector<_Tp, _Alloc>::push_back(const value_type&) [with _Tp = std::__cxx11::basic_string<char>; _Alloc = std::allocator<std::__cxx11::basic_string<char> >; std::vector<_Tp, _Alloc>::value_type = std::__cxx11::basic_string<char>]'  
 1184 |       push_back(const value_type& __x)  
      |       ^~~~~~~~~  
/usr/include/c++/9/bits/stl_vector.h:1184:35: note:   no known conversion for argument 1 from 'std::vector<std::__cxx11::basic_string<char> >' to 'const value_type&' {aka 'const std::__cxx11::basic_string<char>&'}  
 1184 |       push_back(const value_type& __x)  
      |                 ~~~~~~~~~~~~~~~~~~^~~  
/usr/include/c++/9/bits/stl_vector.h:1200:7: note: candidate: 'void std::vector<_Tp, _Alloc>::push_back(std::vector<_Tp, _Alloc>::value_type&&) [with _Tp = std::__cxx11::basic_string<char>; _Alloc = std::allocator<std::__cxx11::basic_string<char> >; std::vector<_Tp, _Alloc>::value_type = std::__cxx11::basic_string<char>]'  
 1200 |       push_back(value_type&& __x)  
      |       ^~~~~~~~~  
/usr/include/c++/9/bits/stl_vector.h:1200:30: note:   no known conversion for argument 1 from 'std::vector<std::__cxx11::basic_string<char> >' to 'std::vector<std::__cxx11::basic_string<char> >::value_type&&' {aka 'std::__cxx11::basic_string<char>&&'}  
 1200 |       push_back(value_type&& __x)  
      |                 ~~~~~~~~~~~~~^~~  
```  
  
Is this use of `operator+=` legitimate? I can't find it in the documentation, but since it used to work I am wondering if the bug here is in Boost or in the using code.  
  
Thanks.

---

## Comment 1

> Username: jeking3  
> Created at: 2022-03-01 16:27:38 UTC  
> Url: https://github.com/boostorg/assign/issues/33#issuecomment-1055623874  

@giomasce is this still an issue / blocking anything?

---

## Comment 2

> Username: giomasce  
> Created at: 2022-03-01 19:55:14 UTC  
> Url: https://github.com/boostorg/assign/issues/33#issuecomment-1055803085  

Hi, the issue was happening in a Debian package (`rivet`) that was in the meantime removed for other reasons, so it's not blocking anything any more on my side. I don't know if the issue was actually due to a Boost bug and, if so, whether it is still present.

---

## Comment 3

> Username: jeking3  
> Created at: 2024-12-28 17:26:37 UTC  
> Url: https://github.com/boostorg/assign/issues/33#issuecomment-2564385738  

Closing; please re-open if problems arise again.
