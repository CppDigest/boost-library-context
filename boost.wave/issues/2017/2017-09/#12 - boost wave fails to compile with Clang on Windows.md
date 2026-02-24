# #12 - boost wave fails to compile with Clang on Windows [Closed]

> Username: hkaiser  
> Created at: 2017-09-02 02:08:07 UTC  
> Updated at: 2017-09-03 02:21:00 UTC  
> Closed at: 2017-09-03 02:21:00 UTC  
> Url: https://github.com/boostorg/wave/issues/12  

boost wave fails to compile with Clang on Windows, there are a lot of issues of type: \boost/wave/grammars/cpp_expression_grammar.hpp:829:18: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]   
  
case T_CPPCOMMENT:  contains newline   
  
```  
Performing configuration checks  
  
    - 32-bit                   : no  (cached)  
    - 64-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : not building  
    - container                : not building  
    - context                  : not building  
    - coroutine                : not building  
    - date_time                : not building  
    - exception                : not building  
    - fiber                    : not building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - iostreams                : not building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - metaparse                : not building  
    - mpi                      : not building  
    - program_options          : not building  
    - python                   : not building  
    - random                   : not building  
    - regex                    : not building  
    - serialization            : not building  
    - signals                  : not building  
    - stacktrace               : not building  
    - system                   : not building  
    - test                     : not building  
    - thread                   : not building  
    - timer                    : not building  
    - type_erasure             : not building  
    - wave                     : building  
  
clang-linux.compile.c++.without-pth bin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi\instantiate_cpp_exprgrammar.obj  
  
  "C:/Program Files/LLVM/bin/clang.exe" -c -x c++ -O0 -g -fno-inline -Wall -g -fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0501 -DBOOST_USE_WINDOWS_H=1 -pthread -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_THREAD_USE_LIB=1 -I"." -o "bin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi\instantiate_cpp_exprgrammar.obj" "libs\wave\src\instantiate_cpp_exprgrammar.cpp"  
  
In file included from libs\wave\src\instantiate_cpp_exprgrammar.cpp:24:  
In file included from .\boost/wave/grammars/cpp_expression_grammar.hpp:17:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:101:23: warning: 'localtime' is deprecated: This function or variable may be unsafe. Consider using localtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::localtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:504:9: note: 'localtime' has been explicitly marked deprecated here  
        _CRT_INSECURE_DEPRECATE(localtime_s)  
        ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_cpp_exprgrammar.cpp:24:  
In file included from .\boost/wave/grammars/cpp_expression_grammar.hpp:17:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:110:23: warning: 'gmtime' is deprecated: This function or variable may be unsafe. Consider using gmtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::gmtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:494:24: note: 'gmtime' has been explicitly marked deprecated here  
        _Check_return_ _CRT_INSECURE_DEPRECATE(gmtime_s)  
                       ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_cpp_exprgrammar.cpp:24:  
.\boost/wave/grammars/cpp_expression_grammar.hpp:829:18: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_CPPCOMMENT:              // contains newline  
                 ^  
libs\wave\src\instantiate_cpp_exprgrammar.cpp:44:40: note: in instantiation of member function 'boost::wave::grammars::expression_grammar_gen<boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::evaluate' requested here  
template struct boost::wave::grammars::expression_grammar_gen<token_type>;  
                                       ^  
In file included from libs\wave\src\instantiate_cpp_exprgrammar.cpp:24:  
.\boost/wave/grammars/cpp_expression_grammar.hpp:828:18: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_EOF:  
                 ^  
.\boost/wave/grammars/cpp_expression_grammar.hpp:827:18: error: case value evaluates to -1342176886, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_NEWLINE:  
                 ^  
.\boost/wave/grammars/cpp_expression_grammar.hpp:824:18: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_CCOMMENT:  
                 ^  
.\boost/wave/grammars/cpp_expression_grammar.hpp:823:18: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_SPACE2:  
                 ^  
.\boost/wave/grammars/cpp_expression_grammar.hpp:822:18: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_SPACE:  
                 ^  
2 warnings and 6 errors generated.  
clang-linux.compile.c++.without-pth bin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi\instantiate_re2c_lexer.obj  
  
  "C:/Program Files/LLVM/bin/clang.exe" -c -x c++ -O0 -g -fno-inline -Wall -g -fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0501 -DBOOST_USE_WINDOWS_H=1 -pthread -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_THREAD_USE_LIB=1 -I"." -o "bin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi\instantiate_re2c_lexer.obj" "libs\wave\src\instantiate_re2c_lexer.cpp"  
  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
In file included from .\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:25:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:101:23: warning: 'localtime' is deprecated: This function or variable may be unsafe. Consider using localtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::localtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:504:9: note: 'localtime' has been explicitly marked deprecated here  
        _CRT_INSECURE_DEPRECATE(localtime_s)  
        ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
In file included from .\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:25:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:110:23: warning: 'gmtime' is deprecated: This function or variable may be unsafe. Consider using gmtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::gmtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:494:24: note: 'gmtime' has been explicitly marked deprecated here  
        _Check_return_ _CRT_INSECURE_DEPRECATE(gmtime_s)  
                       ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:321:5: warning: 'vsprintf' is deprecated: This function or variable may be unsafe. Consider using vsprintf_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
    vsprintf(buffer, msg, params);  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:347:9: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lexer' requested here  
    :   re2c_lexer(first, last, pos, language)  
        ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<  
                                       ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\stdio.h:1798:5: note: 'vsprintf' has been explicitly marked deprecated here  
    __DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST(  
    ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\corecrt.h:731:5: note: expanded from macro '__DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST'  
    __DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST_EX(_ReturnType, _ReturnPolicy, _DeclSpec, _CC, _FuncName, _FuncName##_s, _VFuncName, _VFuncName##_s, _SalAttributeDst, _DstType, _Dst, _TType1, _TArg1)  
    ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\corecrt.h:1815:17: note: expanded from macro '__DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST_EX'  
                _CRT_INSECURE_DEPRECATE(_SecureVFuncName) _DeclSpec _ReturnType _CC _VFuncName(_SalAttributeDst _DstType *_Dst, _TType1 _TArg1, va_list _Args);  
                ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:242:10: error: case value evaluates to -1610612339, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:241:10: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE2:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:240:10: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:239:10: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CPPCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:238:10: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:352:61: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    token_type& get(token_type& result) { return re2c_lexer.get(result); }  
                                                            ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:242:10: error: case value evaluates to -1610612339, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:241:10: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE2:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:240:10: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:239:10: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CPPCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:238:10: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:352:61: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<const char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    token_type& get(token_type& result) { return re2c_lexer.get(result); }  
                                                            ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<const char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<const char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer.cpp:57:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<const char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
fatal error: too many errors emitted, stopping now [-ferror-limit=]  
3 warnings and 20 errors generated.  
clang-linux.compile.c++.without-pth bin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi\instantiate_re2c_lexer_str.obj  
  
  "C:/Program Files/LLVM/bin/clang.exe" -c -x c++ -O0 -g -fno-inline -Wall -g -fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0501 -DBOOST_USE_WINDOWS_H=1 -pthread -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_THREAD_USE_LIB=1 -I"." -o "bin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi\instantiate_re2c_lexer_str.obj" "libs\wave\src\instantiate_re2c_lexer_str.cpp"  
  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
In file included from .\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:25:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:101:23: warning: 'localtime' is deprecated: This function or variable may be unsafe. Consider using localtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::localtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:504:9: note: 'localtime' has been explicitly marked deprecated here  
        _CRT_INSECURE_DEPRECATE(localtime_s)  
        ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
In file included from .\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:25:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:110:23: warning: 'gmtime' is deprecated: This function or variable may be unsafe. Consider using gmtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::gmtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:494:24: note: 'gmtime' has been explicitly marked deprecated here  
        _Check_return_ _CRT_INSECURE_DEPRECATE(gmtime_s)  
                       ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:321:5: warning: 'vsprintf' is deprecated: This function or variable may be unsafe. Consider using vsprintf_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
    vsprintf(buffer, msg, params);  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:347:9: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lexer' requested here  
    :   re2c_lexer(first, last, pos, language)  
        ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer_str.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<std::string::iterator>;  
                                       ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\stdio.h:1798:5: note: 'vsprintf' has been explicitly marked deprecated here  
    __DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST(  
    ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\corecrt.h:731:5: note: expanded from macro '__DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST'  
    __DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST_EX(_ReturnType, _ReturnPolicy, _DeclSpec, _CC, _FuncName, _FuncName##_s, _VFuncName, _VFuncName##_s, _SalAttributeDst, _DstType, _Dst, _TType1, _TArg1)  
    ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\corecrt.h:1815:17: note: expanded from macro '__DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST_EX'  
                _CRT_INSECURE_DEPRECATE(_SecureVFuncName) _DeclSpec _ReturnType _CC _VFuncName(_SalAttributeDst _DstType *_Dst, _TType1 _TArg1, va_list _Args);  
                ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer_str.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<std::string::iterator>;  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:242:10: error: case value evaluates to -1610612339, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:241:10: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE2:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:240:10: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:239:10: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CPPCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:238:10: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:352:61: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    token_type& get(token_type& result) { return re2c_lexer.get(result); }  
                                                            ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer_str.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<std::string::iterator>;  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:242:10: error: case value evaluates to -1610612339, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:241:10: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE2:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:240:10: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:239:10: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CPPCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:238:10: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:352:61: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<std::_String_const_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    token_type& get(token_type& result) { return re2c_lexer.get(result); }  
                                                            ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_const_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_const_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer_str.cpp:56:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<std::_String_const_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<std::string::const_iterator>;  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
fatal error: too many errors emitted, stopping now [-ferror-limit=]  
3 warnings and 20 errors generated.  
...skipped <pbin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi>libboost_wave-clang50-mt-d-1_65.lib(clean) for lack of <pbin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi>instantiate_cpp_exprgrammar.obj...  
...skipped <pbin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi>libboost_wave-clang50-mt-d-1_65.lib for lack of <pbin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi>instantiate_cpp_exprgrammar.obj...  
...skipped <pstage\lib>libboost_wave-clang50-mt-d-1_65.lib for lack of <pbin.v2\libs\wave\build\clang-linux-5.0\debug\link-static\threading-multi>libboost_wave-clang50-mt-d-1_65.lib...  
clang-linux.compile.c++.without-pth bin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi\instantiate_cpp_exprgrammar.obj  
  
  "C:/Program Files/LLVM/bin/clang.exe" -c -x c++ -O3 -Wno-inline -Wall -fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0501 -DBOOST_USE_WINDOWS_H=1 -pthread -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_THREAD_USE_LIB=1 -DNDEBUG -I"." -o "bin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi\instantiate_cpp_exprgrammar.obj" "libs\wave\src\instantiate_cpp_exprgrammar.cpp"  
  
In file included from libs\wave\src\instantiate_cpp_exprgrammar.cpp:24:  
In file included from .\boost/wave/grammars/cpp_expression_grammar.hpp:17:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:101:23: warning: 'localtime' is deprecated: This function or variable may be unsafe. Consider using localtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::localtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:504:9: note: 'localtime' has been explicitly marked deprecated here  
        _CRT_INSECURE_DEPRECATE(localtime_s)  
        ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_cpp_exprgrammar.cpp:24:  
In file included from .\boost/wave/grammars/cpp_expression_grammar.hpp:17:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:110:23: warning: 'gmtime' is deprecated: This function or variable may be unsafe. Consider using gmtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::gmtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:494:24: note: 'gmtime' has been explicitly marked deprecated here  
        _Check_return_ _CRT_INSECURE_DEPRECATE(gmtime_s)  
                       ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_cpp_exprgrammar.cpp:24:  
.\boost/wave/grammars/cpp_expression_grammar.hpp:829:18: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_CPPCOMMENT:              // contains newline  
                 ^  
libs\wave\src\instantiate_cpp_exprgrammar.cpp:44:40: note: in instantiation of member function 'boost::wave::grammars::expression_grammar_gen<boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::evaluate' requested here  
template struct boost::wave::grammars::expression_grammar_gen<token_type>;  
                                       ^  
In file included from libs\wave\src\instantiate_cpp_exprgrammar.cpp:24:  
.\boost/wave/grammars/cpp_expression_grammar.hpp:828:18: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_EOF:  
                 ^  
.\boost/wave/grammars/cpp_expression_grammar.hpp:827:18: error: case value evaluates to -1342176886, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_NEWLINE:  
                 ^  
.\boost/wave/grammars/cpp_expression_grammar.hpp:824:18: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_CCOMMENT:  
                 ^  
.\boost/wave/grammars/cpp_expression_grammar.hpp:823:18: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_SPACE2:  
                 ^  
.\boost/wave/grammars/cpp_expression_grammar.hpp:822:18: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
            case T_SPACE:  
                 ^  
2 warnings and 6 errors generated.  
clang-linux.compile.c++.without-pth bin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi\instantiate_re2c_lexer.obj  
  
  "C:/Program Files/LLVM/bin/clang.exe" -c -x c++ -O3 -Wno-inline -Wall -fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0501 -DBOOST_USE_WINDOWS_H=1 -pthread -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_THREAD_USE_LIB=1 -DNDEBUG -I"." -o "bin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi\instantiate_re2c_lexer.obj" "libs\wave\src\instantiate_re2c_lexer.cpp"  
  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
In file included from .\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:25:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:101:23: warning: 'localtime' is deprecated: This function or variable may be unsafe. Consider using localtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::localtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:504:9: note: 'localtime' has been explicitly marked deprecated here  
        _CRT_INSECURE_DEPRECATE(localtime_s)  
        ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
In file included from .\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:25:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:110:23: warning: 'gmtime' is deprecated: This function or variable may be unsafe. Consider using gmtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::gmtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:494:24: note: 'gmtime' has been explicitly marked deprecated here  
        _Check_return_ _CRT_INSECURE_DEPRECATE(gmtime_s)  
                       ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:321:5: warning: 'vsprintf' is deprecated: This function or variable may be unsafe. Consider using vsprintf_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
    vsprintf(buffer, msg, params);  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:347:9: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lexer' requested here  
    :   re2c_lexer(first, last, pos, language)  
        ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<  
                                       ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\stdio.h:1798:5: note: 'vsprintf' has been explicitly marked deprecated here  
    __DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST(  
    ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\corecrt.h:731:5: note: expanded from macro '__DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST'  
    __DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST_EX(_ReturnType, _ReturnPolicy, _DeclSpec, _CC, _FuncName, _FuncName##_s, _VFuncName, _VFuncName##_s, _SalAttributeDst, _DstType, _Dst, _TType1, _TArg1)  
    ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\corecrt.h:1815:17: note: expanded from macro '__DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST_EX'  
                _CRT_INSECURE_DEPRECATE(_SecureVFuncName) _DeclSpec _ReturnType _CC _VFuncName(_SalAttributeDst _DstType *_Dst, _TType1 _TArg1, va_list _Args);  
                ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:242:10: error: case value evaluates to -1610612339, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:241:10: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE2:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:240:10: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:239:10: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CPPCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:238:10: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:352:61: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    token_type& get(token_type& result) { return re2c_lexer.get(result); }  
                                                            ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:242:10: error: case value evaluates to -1610612339, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:241:10: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE2:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:240:10: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:239:10: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CPPCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:238:10: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:352:61: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<const char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    token_type& get(token_type& result) { return re2c_lexer.get(result); }  
                                                            ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<const char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<const char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer.cpp:57:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<const char *, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
fatal error: too many errors emitted, stopping now [-ferror-limit=]  
3 warnings and 20 errors generated.  
clang-linux.compile.c++.without-pth bin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi\instantiate_re2c_lexer_str.obj  
  
  "C:/Program Files/LLVM/bin/clang.exe" -c -x c++ -O3 -Wno-inline -Wall -fmsc-version=1910 -DBOOST_USE_WINAPI_VERSION=0x0501 -DBOOST_USE_WINDOWS_H=1 -pthread -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_THREAD_USE_LIB=1 -DNDEBUG -I"." -o "bin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi\instantiate_re2c_lexer_str.obj" "libs\wave\src\instantiate_re2c_lexer_str.cpp"  
  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
In file included from .\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:25:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:101:23: warning: 'localtime' is deprecated: This function or variable may be unsafe. Consider using localtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::localtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:504:9: note: 'localtime' has been explicitly marked deprecated here  
        _CRT_INSECURE_DEPRECATE(localtime_s)  
        ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
In file included from .\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:25:  
In file included from .\boost/spirit/include/classic_core.hpp:11:  
In file included from .\boost/spirit/home/classic/core.hpp:42:  
In file included from .\boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from .\boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:17:  
In file included from .\boost/thread/mutex.hpp:14:  
In file included from .\boost/thread/win32/mutex.hpp:9:  
In file included from .\boost/thread/win32/basic_timed_mutex.hpp:16:  
In file included from .\boost/thread/thread_time.hpp:9:  
In file included from .\boost/date_time/time_clock.hpp:16:  
.\boost/date_time/c_time.hpp:110:23: warning: 'gmtime' is deprecated: This function or variable may be unsafe. Consider using gmtime_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
        result = std::gmtime(t);  
                      ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\time.h:494:24: note: 'gmtime' has been explicitly marked deprecated here  
        _Check_return_ _CRT_INSECURE_DEPRECATE(gmtime_s)  
                       ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:321:5: warning: 'vsprintf' is deprecated: This function or variable may be unsafe. Consider using vsprintf_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Wdeprecated-declarations]  
    vsprintf(buffer, msg, params);  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:347:9: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lexer' requested here  
    :   re2c_lexer(first, last, pos, language)  
        ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer_str.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<std::string::iterator>;  
                                       ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\stdio.h:1798:5: note: 'vsprintf' has been explicitly marked deprecated here  
    __DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST(  
    ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\corecrt.h:731:5: note: expanded from macro '__DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST'  
    __DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST_EX(_ReturnType, _ReturnPolicy, _DeclSpec, _CC, _FuncName, _FuncName##_s, _VFuncName, _VFuncName##_s, _SalAttributeDst, _DstType, _Dst, _TType1, _TArg1)  
    ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\ucrt\corecrt.h:1815:17: note: expanded from macro '__DEFINE_CPP_OVERLOAD_STANDARD_FUNC_0_1_ARGLIST_EX'  
                _CRT_INSECURE_DEPRECATE(_SecureVFuncName) _DeclSpec _ReturnType _CC _VFuncName(_SalAttributeDst _DstType *_Dst, _TType1 _TArg1, va_list _Args);  
                ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:251:55: note: expanded from macro '_CRT_INSECURE_DEPRECATE'  
        #define _CRT_INSECURE_DEPRECATE(_Replacement) _CRT_DEPRECATE_TEXT(    \  
                                                      ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.11.25503\include\vcruntime.h:241:47: note: expanded from macro '_CRT_DEPRECATE_TEXT'  
#define _CRT_DEPRECATE_TEXT(_Text) __declspec(deprecated(_Text))  
                                              ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer_str.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<std::string::iterator>;  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:242:10: error: case value evaluates to -1610612339, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:241:10: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE2:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:240:10: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:239:10: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CPPCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:238:10: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:352:61: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    token_type& get(token_type& result) { return re2c_lexer.get(result); }  
                                                            ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer_str.cpp:55:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<std::string::iterator>;  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:242:10: error: case value evaluates to -1610612339, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:241:10: error: case value evaluates to -805305975, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE2:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:240:10: error: case value evaluates to -805305976, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_SPACE:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:239:10: error: case value evaluates to -804257404, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CPPCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:238:10: error: case value evaluates to -804257405, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_CCOMMENT:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:272:10: error: case value evaluates to -1608515187, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_ANY_TRIGRAPH:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:352:61: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lexer<std::_String_const_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    token_type& get(token_type& result) { return re2c_lexer.get(result); }  
                                                            ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:345:5: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_const_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::get' requested here  
    lex_functor(IteratorT const &first, IteratorT const &last,  
    ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:414:16: note: in instantiation of member function 'boost::wave::cpplexer::re2clex::lex_functor<std::_String_const_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::lex_functor' requested here  
    return new lex_functor<IteratorT, PositionT, TokenT>(first, last, pos, language);  
               ^  
libs\wave\src\instantiate_re2c_lexer_str.cpp:56:40: note: in instantiation of member function 'boost::wave::cpplexer::new_lexer_gen<std::_String_const_iterator<std::_String_val<std::_Simple_types<char> > >, boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > >, boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char *> > > > >::new_lexer' requested here  
template struct boost::wave::cpplexer::new_lexer_gen<std::string::const_iterator>;  
                                       ^  
In file included from libs\wave\src\instantiate_re2c_lexer_str.cpp:29:  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:248:10: error: case value evaluates to -1073741423, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_EOF:  
         ^  
.\boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:243:10: error: case value evaluates to -536346221, which cannot be narrowed to type 'unsigned int' [-Wc++11-narrowing]  
    case T_PP_NUMBER:  
         ^  
fatal error: too many errors emitted, stopping now [-ferror-limit=]  
3 warnings and 20 errors generated.  
...skipped <pbin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi>libboost_wave-clang50-mt-1_65.lib(clean) for lack of <pbin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi>instantiate_cpp_exprgrammar.obj...  
...skipped <pbin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi>libboost_wave-clang50-mt-1_65.lib for lack of <pbin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi>instantiate_cpp_exprgrammar.obj...  
...skipped <pstage\lib>libboost_wave-clang50-mt-1_65.lib for lack of <pbin.v2\libs\wave\build\clang-linux-5.0\release\link-static\threading-multi>libboost_wave-clang50-mt-1_65.lib...  
...failed updating 6 targets...  
```

---

## Comment 1

> Username: SSE4  
> Created at: 2017-09-02 05:52:59 UTC  
> Url: https://github.com/boostorg/wave/issues/12#issuecomment-326724272  

@hkaiser note that it compiles if I add "-Wno-c++11-narrowing", however, I am not sure will it work correctly in that case (e.g. will enumerations resolve to the expected value).  
I am planning to run full boost test against Clang on Windows as soon as all compilation issues are resolved.

---

## Comment 2

> Username: hkaiser  
> Created at: 2017-09-02 18:10:12 UTC  
> Url: https://github.com/boostorg/wave/issues/12#issuecomment-326760897  

@SSE4: please try #13. For me this fixes the compilation problems, I now however see a ICE in clang/c2 while compiling the wave tool (I have not tried the binary from the LLVM website).

---

## Comment 3

> Username: SSE4  
> Created at: 2017-09-03 02:19:25 UTC  
> Url: https://github.com/boostorg/wave/issues/12#issuecomment-326780097  

@hkaiser yes, it worked, thanks a lot for the quick fix!  
P.S. I also had ICEs in Clang 4.0.1, so I had to update to Clang 5.0.0 RC4 which doesn't have ICEs. didn't try Clang/C2 as Microsoft decided to discontinue it.
