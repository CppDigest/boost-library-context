# #2482 - Errors from Boost date_time with Visual Studio 2022 compiling with any C++ language standard [Closed]

> Username: hamzaahmedzia1  
> Created at: 2022-07-12 15:29:20 UTC  
> Updated at: 2022-07-14 13:09:21 UTC  
> Closed at: 2022-07-14 12:46:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2482  

### Version of Beast  
https://www.boost.org/users/history/version_1_79_0.html  
  
### Steps necessary to reproduce the problem  
  
```cpp  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/strand.hpp>  
```  
  
### All relevant compiler information  
  
- Visual Studio 2022 v 143  
- Windows SDK version 10.0.22621.0  
  
I am on a 64 bit Windows 11 Professional system running 64 bit Visual Studio 2022 version 17.2.5 Community Edition.  
  
I am working on a project which uses boost asio and beast (version 1_79_0) which does not compile regardless whether the "C++ Language Standard" property set to `/std:c++14` or `/std:c++17` or `/std:c++20` or `/std:c++latest` (preview).  
  
Both Visual Studio and Boost are "fresh installs".   
  
Errors  
  
```console  
- 1>G:\boost_1_79_0\boost\date_time\wrapping_int.hpp(40,28): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\wrapping_int.hpp(40,28): error C2062: type 'int' unexpected  
1>G:\boost_1_79_0\boost\date_time\wrapping_int.hpp(88): message : see reference to class template instantiation 'boost::date_time::wrapping_int<int_type_,wrap_val>' being compiled  
1>G:\boost_1_79_0\boost\date_time\wrapping_int.hpp(40,1): error C2334: unexpected token(s) preceding '{'; skipping apparent function body  
1>G:\boost_1_79_0\boost\date_time\wrapping_int.hpp(114,28): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\wrapping_int.hpp(114,28): error C2062: type 'int' unexpected  
1>G:\boost_1_79_0\boost\date_time\wrapping_int.hpp(161): message : see reference to class template instantiation 'boost::date_time::wrapping_int2<int_type_,wrap_min,wrap_max>' being compiled  
1>G:\boost_1_79_0\boost\date_time\wrapping_int.hpp(114,1): error C2334: unexpected token(s) preceding '{'; skipping apparent function body  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(84,33): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(111,33): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): error C2760: syntax error: '*' was unexpected here; expected 'id-expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(116): message : see reference to class template instantiation 'boost::date_time::month_functor<date_type>' being compiled  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): error C2760: syntax error: '*' was unexpected here; expected 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): error C3878: syntax error: unexpected token '*' following 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): message : error recovery skipped: '*'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): error C2760: syntax error: ')' was unexpected here; expected ';'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): error C3878: syntax error: unexpected token ')' following 'expression_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): message : error recovery skipped: ')'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): error C2760: syntax error: ')' was unexpected here; expected 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(75,75): message : error recovery skipped: ') ) )  . . . )'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C2760: syntax error: '*' was unexpected here; expected 'id-expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C3878: syntax error: unexpected token '(' following 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): message : error recovery skipped: '( ( int  . . . *'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C2760: syntax error: ')' was unexpected here; expected ';'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C3878: syntax error: unexpected token ')' following 'jump_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C3878: syntax error: unexpected token ')' following 'statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C3878: syntax error: unexpected token ')' following 'statement_seq'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): message : missing one of:  '}' ?  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C2760: syntax error: ')' was unexpected here; expected '}'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C3878: syntax error: unexpected token ')' following 'compound_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C3878: syntax error: unexpected token ')' following 'selection_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): message : error recovery skipped: ')'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C2760: syntax error: ')' was unexpected here; expected 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): error C3878: syntax error: unexpected token ')' following 'expression_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,35): message : error recovery skipped: ') ) ,'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,67): error C2760: syntax error: ')' was unexpected here; expected ';'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,67): error C3878: syntax error: unexpected token ')' following 'expression_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(78,67): message : error recovery skipped: ')'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): error C2760: syntax error: '*' was unexpected here; expected 'id-expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): error C2760: syntax error: '*' was unexpected here; expected 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): error C3878: syntax error: unexpected token '*' following 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): message : error recovery skipped: '*'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): error C2760: syntax error: ')' was unexpected here; expected ';'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): error C3878: syntax error: unexpected token ')' following 'expression_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): message : error recovery skipped: ')'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): error C2760: syntax error: ')' was unexpected here; expected 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(102,75): message : error recovery skipped: ') ) )  . . . )'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C2760: syntax error: '*' was unexpected here; expected 'id-expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C3878: syntax error: unexpected token '(' following 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): message : error recovery skipped: '( ( int  . . . *'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C2760: syntax error: ')' was unexpected here; expected ';'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C3878: syntax error: unexpected token ')' following 'jump_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C3878: syntax error: unexpected token ')' following 'statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C3878: syntax error: unexpected token ')' following 'statement_seq'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): message : missing one of:  '}' ?  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C2760: syntax error: ')' was unexpected here; expected '}'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C3878: syntax error: unexpected token ')' following 'compound_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C3878: syntax error: unexpected token ')' following 'selection_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): message : error recovery skipped: ')'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C2760: syntax error: ')' was unexpected here; expected 'expression'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): error C3878: syntax error: unexpected token ')' following 'expression_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,35): message : error recovery skipped: ') ) ,'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,67): error C2760: syntax error: ')' was unexpected here; expected ';'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,67): error C3878: syntax error: unexpected token ')' following 'expression_statement'  
1>G:\boost_1_79_0\boost\date_time\adjust_functors.hpp(105,67): message : error recovery skipped: ')'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): warning C4003: not enough arguments for function-like macro invocation 'as_int'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): error C2760: syntax error: '*' was unexpected here; expected 'id-expression'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(221): message : see reference to class template instantiation 'boost::date_time::split_timedate_system<config>' being compiled  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): error C3878: syntax error: unexpected token '(' following 'expression'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): message : error recovery skipped: '( ( int  . . . *'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): error C2760: syntax error: ')' was unexpected here; expected ';'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): error C3878: syntax error: unexpected token ')' following 'jump_statement'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): message : error recovery skipped: ')'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): error C2760: syntax error: ')' was unexpected here; expected 'expression'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): error C3878: syntax error: unexpected token ')' following 'expression_statement'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(185,64): message : error recovery skipped: ') ) )  . . . )'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): error C2760: syntax error: '*' was unexpected here; expected 'id-expression'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): error C3878: syntax error: unexpected token '(' following 'expression'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): message : error recovery skipped: '( ( int  . . . *'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): error C2760: syntax error: ')' was unexpected here; expected ';'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): error C3878: syntax error: unexpected token ')' following 'jump_statement'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): message : error recovery skipped: ')'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): error C2760: syntax error: ')' was unexpected here; expected 'expression'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): error C3878: syntax error: unexpected token ')' following 'expression_statement'  
1>G:\boost_1_79_0\boost\date_time\time_system_split.hpp(203,64): message : error recovery skipped: ') ) )  . . . )'  
```  
  
How to resolve?

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-07-12 18:00:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2482#issuecomment-1182139276  

It looks to me as if you've used one of the older asio timer types that is based on boost::date_time rather than std::chrono.  
However this isn't the issue. With seeing any source code, I would guess that you've put a `#include` directive inside a `namespace` directive.  
  
Are you able to share a minimal version of the source code on https://godbolt.org/ that demonstrates the problem?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-07-13 03:11:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2482#issuecomment-1182715109  

@hamzaahmedzia1,  
  
```cpp  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/strand.hpp>  
```  
  
are definitely not sufficient steps to reproduce the problem.

---

## Comment 3

> Username: hamzaahmedzia1  
> Created at: 2022-07-13 10:06:41 UTC  
> Updated at: 2022-07-13 10:48:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2482#issuecomment-1183027888  

>It looks to me as if you've used one of the older asio timer types that is based on boost::date_time rather than std::chrono.  
However this isn't the issue. With seeing any source code, I would guess that you've put a #include directive inside a namespace directive.  
  
I am using the http_client_async.cpp class I found at \libs\beast\example\http\client\async\http_client_async.cpp  
  
> Are you able to share a minimal version of the source code on https://godbolt.org/ that demonstrates the problem?  
  
>are definitely not sufficient steps to reproduce the problem.  
  
I could zip my visual studio project folder to a private url (not public like godbolt). Can you provide any such url?  
  
I would like to request you to keep my project private.

---

## Comment 4

> Username: hamzaahmedzia1  
> Created at: 2022-07-14 12:20:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2482#issuecomment-1184380339  

Resolved the issue by re-ordering includes.  
  
A framework header file I was including before boost header files was causing the problem. Pushed that include to the end and the errors disappeared.

---

## Comment 5

> Username: sehe  
> Created at: 2022-07-14 13:09:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2482#issuecomment-1184428805  

Thanks for the update. You might want to report the issue to the developers of the framework header.
