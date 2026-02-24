# #184 - clang-tidy error: Null pointer passed as an argument to strlen [Open]

> Username: erenon  
> Created at: 2020-11-12 13:47:48 UTC  
> Updated at: 2020-11-12 13:47:48 UTC  
> Url: https://github.com/boostorg/process/issues/184  

```  
/home/runner/work/binlog/binlog/test/integration/IntegrationTest.cpp:168:13: note: Calling constructor for 'child'  
  bp::child bread(g_bread_path, "-f", "%m", bp::std_in < binary, bp::std_out > text);  
            ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/child.hpp:35:13: note: Calling 'execute_impl'  
    : child(::boost::process::detail::execute_impl(std::forward<Args>(args)...)) {}  
            ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/execute_impl.hpp:275:12: note: Calling 'basic_execute_impl'  
    return basic_execute_impl<req_char_type>(  
           ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/execute_impl.hpp:267:12: note: Calling 'executor::operator()'  
    return exec();  
           ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:308:16: note: Calling 'executor::invoke'  
        return invoke(has_ignore_error(), shall_use_vfork());  
               ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:380:13: note: Assuming the condition is false  
        if (::pipe(p.p) == -1)  
            ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:380:9: note: Taking false branch  
        if (::pipe(p.p) == -1)  
        ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:385:13: note: Assuming the condition is false  
        if (::fcntl(p.p[1], F_SETFD, FD_CLOEXEC) == -1)  
            ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:385:9: note: Taking false branch  
        if (::fcntl(p.p[1], F_SETFD, FD_CLOEXEC) == -1)  
        ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:394:9: note: Taking false branch  
        if (_ec)  
        ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:400:13: note: Assuming the condition is true  
        if (cmd_style)  
            ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:400:9: note: Taking true branch  
        if (cmd_style)  
        ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:401:13: note: Calling 'executor::prepare_cmd_style'  
            prepare_cmd_style();  
            ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:274:13: note: Left side of '&&' is true  
        if ((prepare_cmd_style_fn.find('/') == std::string::npos) && ::access(prepare_cmd_style_fn.c_str(), X_OK))  
            ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:274:70: note: Assuming the condition is true  
        if ((prepare_cmd_style_fn.find('/') == std::string::npos) && ::access(prepare_cmd_style_fn.c_str(), X_OK))  
                                                                     ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:274:9: note: Taking true branch  
        if ((prepare_cmd_style_fn.find('/') == std::string::npos) && ::access(prepare_cmd_style_fn.c_str(), X_OK))  
        ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:277:21: note: Assuming pointer value is null  
            while ((*e != nullptr) && !boost::starts_with(*e, "PATH="))  
                    ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:277:21: note: Assuming the condition is false  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:277:36: note: Left side of '&&' is false  
            while ((*e != nullptr) && !boost::starts_with(*e, "PATH="))  
                                   ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:280:13: note: Taking true branch  
            if (e != nullptr)  
            ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/process/detail/posix/executor.hpp:283:17: note: Calling 'split'  
                boost::split(path, *e, boost::is_any_of(":"));  
                ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/algorithm/string/split.hpp:158:20: note: Calling 'iter_split'  
            return ::boost::algorithm::iter_split(  
                   ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/algorithm/string/iter_find.hpp:161:110: note: Passing value via 1st parameter 'r'  
            iterator_range<BOOST_STRING_TYPENAME range_iterator<RangeT>::type> lit_input(::boost::as_literal(Input));  
                                                                                                             ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/algorithm/string/iter_find.hpp:161:90: note: Calling 'as_literal'  
            iterator_range<BOOST_STRING_TYPENAME range_iterator<RangeT>::type> lit_input(::boost::as_literal(Input));  
                                                                                         ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/range/as_literal.hpp:145:42: note: Passing null pointer value via 1st parameter 'r'  
        return range_detail::make_range( r, range_detail::is_char_ptr(r) );  
                                         ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/range/as_literal.hpp:145:16: note: Calling 'make_range'  
        return range_detail::make_range( r, range_detail::is_char_ptr(r) );  
               ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/range/as_literal.hpp:129:54: note: Passing null pointer value via 1st parameter 's'  
            return iterator_range<T*>( r, r + length(r) );  
                                                     ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/range/as_literal.hpp:129:47: note: Calling 'length'  
            return iterator_range<T*>( r, r + length(r) );  
                                              ^  
/opt/hostedtoolcache/boost/1.72.0/x64/include/boost/range/as_literal.hpp:43:20: note: Null pointer passed as an argument to a 'nonnull' parameter  
            return strlen( s );  
```  
  
  
https://github.com/Morgan-Stanley/binlog/pull/96/checks?check_run_id=1390629482
