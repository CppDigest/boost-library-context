# #146 - [With example] Make default constructible [Open]

> Username: Spixmaster  
> Created at: 2025-12-05 10:24:11 UTC  
> Updated at: 2025-12-09 09:46:30 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/146  

Can this be fixed or is this a specific error with `std::format()` and `std::vformat()`?  
  
This source code fails. By the way, the same error happens with `std::format()`.  
  
```cpp  
std::string task_advertisement_verification(const boost::safe_numerics::safe<std::int32_t> &advertisement)  
{  
    return std::vformat(  
      boost::locale::translate(R"(Verify the advertisement with identifier "{}".)").str(),  
      std::make_format_args(advertisement));  
}  
```  
  
Error message:  
```sh  
❯ make  
[  0%] Building CXX object CMakeFiles/argo-tests.dir/src/message/gui.cpp.o  
In file included from /usr/include/c++/15.2.1/ostream:44,  
                 from /usr/include/c++/15.2.1/istream:43,  
                 from /usr/include/boost/safe_numerics/safe_base_operations.hpp:13,  
                 from /usr/include/boost/safe_numerics/safe_integer.hpp:16,  
                 from /home/matheus/programmieren/c++/argo/include/message/gui.hpp:4,  
                 from /home/matheus/programmieren/c++/argo/src/message/gui.cpp:1:  
/usr/include/c++/15.2.1/format: In instantiation of 'static std::__format::_Arg_store<_Context, _Args>::_Element_t std::__format::_Arg_store<_Context, _Args>::_S_make_elt(_Tp&) [with _Tp = const boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> >; _Context = std::basic_format_context<std::__format::_Sink_iter<char>, char>; _Args = {std::basic_format_arg<std::basic_format_context<std::__format::_Sink_iter<char>, char> >::handle}; _Element_t = std::__format::_Arg_store<std::basic_format_context<std::__format::_Sink_iter<char>, char>, std::basic_format_arg<std::basic_format_context<std::__format::_Sink_iter<char>, char> >::handle>::_Element_t]':  
/usr/include/c++/15.2.1/format:4290:23:   required from 'std::__format::_Arg_store<_Context, _Args>::_Arg_store(_Tp& ...) [with _Tp = {const boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> >}; _Context = std::basic_format_context<std::__format::_Sink_iter<char>, char>; _Args = {std::basic_format_arg<std::basic_format_context<std::__format::_Sink_iter<char>, char> >::handle}]'  
 4290 |         : _M_args{_S_make_elt(__a)...}  
      |                   ~~~~~~~~~~~^~~~~  
/usr/include/c++/15.2.1/format:4340:14:   required from 'auto std::make_format_args(_Args& ...) [with _Context = basic_format_context<__format::_Sink_iter<char>, char>; _Args = {const boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> >}]'  
 4340 |       return _Store(__fmt_args...);  
      |              ^~~~~~~~~~~~~~~~~~~~~  
/home/matheus/programmieren/c++/argo/src/message/gui.cpp:119:32:   required from here  
  119 |           std::make_format_args(advertisement));  
      |           ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~  
/usr/include/c++/15.2.1/format:4268:25: error: static assertion failed: std::formatter must be specialized for the type of each format arg  
 4268 |           static_assert(is_default_constructible_v<formatter<_Tq, _CharT>>,  
      |                         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/c++/15.2.1/format:4268:25: note: 'std::is_default_constructible_v<std::formatter<boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> >, char> >' evaluates to false  
make[2]: *** [CMakeFiles/argo-tests.dir/build.make:2249: CMakeFiles/argo-tests.dir/src/message/gui.cpp.o] Error 1  
make[1]: *** [CMakeFiles/Makefile2:133: CMakeFiles/argo-tests.dir/all] Error 2  
make: *** [Makefile:146: all] Error 2  
```
