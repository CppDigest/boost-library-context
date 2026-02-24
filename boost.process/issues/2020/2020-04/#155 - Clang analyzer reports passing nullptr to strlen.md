# #155 - Clang analyzer reports passing nullptr to strlen [Closed]

> Username: jonesmz  
> Created at: 2020-04-17 16:44:03 UTC  
> Updated at: 2020-05-21 08:30:42 UTC  
> Closed at: 2020-05-21 06:11:19 UTC  
> Url: https://github.com/boostorg/process/issues/155  

> /usr/include/boost/process/child.hpp:35:13: note: Calling 'execute_impl<boost::asio::io_context &, std::vector<std::__cxx11::basic_string<char>, std::allocator<std::__cxx11::basic_string<char> > >, boost::process::detail::async_system_handler<(lambda at /srv/jenkins_work/workspace/PR-160/exe/watchdog/unit_tests/watchdog_tests.cpp:129:19)> &>'  
>   
>     : child(::boost::process::detail::execute_impl(std::forward<Args>(args)...)) {}  
>   
>             ^  
>   
> /usr/include/boost/process/detail/execute_impl.hpp:275:12: note: Calling 'basic_execute_impl<char, boost::asio::io_context &, std::vector<std::__cxx11::basic_string<char>, std::allocator<std::__cxx11::basic_string<char> > >, boost::process::detail::async_system_handler<(lambda at /srv/jenkins_work/workspace/PR-160/exe/watchdog/unit_tests/watchdog_tests.cpp:129:19)> &>'  
>   
>     return basic_execute_impl<req_char_type>(  
>   
>            ^  
>   
> /usr/include/boost/process/detail/execute_impl.hpp:267:12: note: Calling 'executor::operator()'  
>   
>     return exec();  
>   
>            ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:308:16: note: Calling 'executor::invoke'  
>   
>         return invoke(has_ignore_error(), shall_use_vfork());  
>   
>                ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:380:13: note: Assuming the condition is false  
>   
>         if (::pipe(p.p) == -1)  
>   
>             ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:380:9: note: Taking false branch  
>   
>         if (::pipe(p.p) == -1)  
>   
>         ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:385:13: note: Assuming the condition is false  
>   
>         if (::fcntl(p.p[1], F_SETFD, FD_CLOEXEC) == -1)  
>   
>             ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:385:9: note: Taking false branch  
>   
>         if (::fcntl(p.p[1], F_SETFD, FD_CLOEXEC) == -1)  
>   
>         ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:394:9: note: Taking false branch  
>   
>         if (_ec)  
>   
>         ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:400:13: note: Field 'cmd_style' is true  
>   
>         if (cmd_style)  
>   
>             ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:400:9: note: Taking true branch  
>   
>         if (cmd_style)  
>   
>         ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:401:13: note: Calling 'executor::prepare_cmd_style'  
>   
>             prepare_cmd_style();  
>   
>             ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:274:14: note: Assuming the condition is true  
>   
>         if ((prepare_cmd_style_fn.find('/') == std::string::npos) && ::access(prepare_cmd_style_fn.c_str(), X_OK))  
>   
>              ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:274:13: note: Left side of '&&' is true  
>   
>         if ((prepare_cmd_style_fn.find('/') == std::string::npos) && ::access(prepare_cmd_style_fn.c_str(), X_OK))  
>   
>             ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:274:70: note: Assuming the condition is true  
>   
>         if ((prepare_cmd_style_fn.find('/') == std::string::npos) && ::access(prepare_cmd_style_fn.c_str(), X_OK))  
>   
>                                                                      ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:274:9: note: Taking true branch  
>   
>         if ((prepare_cmd_style_fn.find('/') == std::string::npos) && ::access(prepare_cmd_style_fn.c_str(), X_OK))  
>   
>         ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:277:21: note: Assuming pointer value is null  
>   
>             while ((*e != nullptr) && !boost::starts_with(*e, "PATH="))  
>   
>                     ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:277:21: note: Assuming the condition is false  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:277:36: note: Left side of '&&' is false  
>   
>             while ((*e != nullptr) && !boost::starts_with(*e, "PATH="))  
>   
>                                    ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:280:13: note: Taking true branch  
>   
>             if (e != nullptr)  
>   
>             ^  
>   
> /usr/include/boost/process/detail/posix/executor.hpp:283:17: note: Calling 'split<std::vector<std::__cxx11::basic_string<char>, std::allocator<std::__cxx11::basic_string<char> > >, char *&, boost::algorithm::detail::is_any_ofF<char>>'  
>   
>                 boost::split(path, *e, boost::is_any_of(":"));  
>   
>                 ^  
>   
> /usr/include/boost/algorithm/string/split.hpp:158:20: note: Calling 'iter_split<std::vector<std::__cxx11::basic_string<char>, std::allocator<std::__cxx11::basic_string<char> > >, char *&, boost::algorithm::detail::token_finderF<boost::algorithm::detail::is_any_ofF<char> >>'  
>   
>             return ::boost::algorithm::iter_split(  
>   
>                    ^  
>   
> /usr/include/boost/algorithm/string/iter_find.hpp:161:110: note: Passing value via 1st parameter 'r'  
>   
>             iterator_range<BOOST_STRING_TYPENAME range_iterator<RangeT>::type> lit_input(::boost::as_literal(Input));  
>   
>                                                                                                              ^  
>   
> /usr/include/boost/algorithm/string/iter_find.hpp:161:90: note: Calling 'as_literal<char *>'  
>   
>             iterator_range<BOOST_STRING_TYPENAME range_iterator<RangeT>::type> lit_input(::boost::as_literal(Input));  
>   
>                                                                                          ^  
>   
> /usr/include/boost/range/as_literal.hpp:145:42: note: Passing null pointer value via 1st parameter 'r'  
>   
>         return range_detail::make_range( r, range_detail::is_char_ptr(r) );  
>   
>                                          ^  
>   
> /usr/include/boost/range/as_literal.hpp:145:16: note: Calling 'make_range<char>'  
>   
>         return range_detail::make_range( r, range_detail::is_char_ptr(r) );  
>   
>                ^  
>   
> /usr/include/boost/range/as_literal.hpp:129:54: note: Passing null pointer value via 1st parameter 's'  
>   
>             return iterator_range<T*>( r, r + length(r) );  
>   
>                                                      ^  
>   
> /usr/include/boost/range/as_literal.hpp:129:47: note: Calling 'length'  
>   
>             return iterator_range<T*>( r, r + length(r) );  
>   
>                                               ^  
>   
> /usr/include/boost/range/as_literal.hpp:43:20: note: Null pointer passed as an argument to a 'nonnull' parameter  
>   
>             return strlen( s );  
>   
>                    ^

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-05-21 06:11:19 UTC  
> Url: https://github.com/boostorg/process/issues/155#issuecomment-631903014  

That seems to be an issue in the iterator library.

---

## Comment 2

> Username: jonesmz  
> Created at: 2020-05-21 07:28:10 UTC  
> Url: https://github.com/boostorg/process/issues/155#issuecomment-631931758  

Dude.  
  
Don't close issues just because you can pass the buck. That's not cool.
