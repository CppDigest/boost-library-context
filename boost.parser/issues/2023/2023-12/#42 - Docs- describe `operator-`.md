# #42 - Docs: describe `operator>` [Closed]

> Username: akrzemi1  
> Created at: 2023-12-31 14:44:59 UTC  
> Updated at: 2024-01-08 04:55:26 UTC  
> Closed at: 2024-01-08 04:55:26 UTC  
> Url: https://github.com/boostorg/parser/issues/42  

It is not clear from the docs when one would want to use `operator>`. Table [Combining Operations and Their Semantics](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/combining_operations.html) says it is for disabling backtracking. But why would I need to disable backtracking?  
  
Section [Error Handling and Debugging](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/error_handling_and_debugging.html)) says:  
  
>  Consider what happens when we fail to parse at an expectation point (created using `operator>()`).  
  
What is _an expectation point_? Is there some connection between `operator>()` and expectation points?
