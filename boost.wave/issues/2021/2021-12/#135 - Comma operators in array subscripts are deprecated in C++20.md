# #135 - Comma operators in array subscripts are deprecated in C++20 [Closed]

> Username: jefftrull  
> Created at: 2021-12-08 19:06:28 UTC  
> Updated at: 2022-02-05 23:44:11 UTC  
> Closed at: 2022-02-05 23:44:11 UTC  
> Url: https://github.com/boostorg/wave/issues/135  

@jeffgarland points out on the Boost mailing list (April 2021) that Wave gets deprecation warnings:  
  
```  
./boost/wave/grammars/cpp_intlit_grammar.hpp:104:53: warning: top-level comma expression in array subscript is deprecated [-Wcomma-subscript]  
```  
This is due to the use of Phoenix semantic actions in a Spirit Classic grammar.
