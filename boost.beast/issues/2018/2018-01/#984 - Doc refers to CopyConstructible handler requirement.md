# #984 - Doc refers to CopyConstructible handler requirement [Closed]

> Username: vinniefalco  
> Created at: 2018-01-16 08:41:58 UTC  
> Updated at: 2018-02-22 00:54:29 UTC  
> Closed at: 2018-02-22 00:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/984  

This should only be **MoveConstructible**  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_io/writing_composed_operations.html  
  
The `echo_op` example should be edited to require only move construction.
