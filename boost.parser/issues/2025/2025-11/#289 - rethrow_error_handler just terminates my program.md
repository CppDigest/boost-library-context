# #289 - rethrow_error_handler just terminates my program [Closed]

> Username: andreasbuhr  
> Created at: 2025-11-07 12:46:02 UTC  
> Updated at: 2026-01-25 23:35:11 UTC  
> Closed at: 2026-01-25 23:35:11 UTC  
> Url: https://github.com/boostorg/parser/issues/289  

Hi,  
  
maybe I am missing something, but based on the documentation provided, I do not understand how `rethrow_error_handler` is supposed to be used.  
  
Whenever a parser fails, `if_full_parse` checks whether the full input was consumed. If not, it executes `throw` without any arguments. However, `throw` without any arguments terminates my program: At that time, no exception is handled.  
  
The single `throw` in https://github.com/boostorg/parser/blob/647cec66831407742a6ad78582f2a9f3cd7d44d3/include/boost/parser/parser.hpp#L2854 terminates my program, in accordance with the standard https://eel.is/c++draft/expr.throw#3 .
