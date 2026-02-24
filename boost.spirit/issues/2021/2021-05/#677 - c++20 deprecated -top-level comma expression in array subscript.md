# #677 - c++20 deprecated "top-level comma expression in array subscript" [Closed]

> Username: nickhuang99  
> Created at: 2021-05-12 20:22:55 UTC  
> Updated at: 2025-09-05 06:00:57 UTC  
> Closed at: 2025-09-05 06:00:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/677  

https://github.com/boostorg/spirit/blob/32b40e9f82f90c00ebc86884c03c09bf2912009a/example/lex/word_count_lexer.cpp#L90  
            _=   word  [++ref(w), ref(c) += distance(_start, _end)]_  
https://github.com/boostorg/spirit/blob/32b40e9f82f90c00ebc86884c03c09bf2912009a/example/lex/word_count_lexer.cpp#L91  
            _|   eol   [++ref(c), ++ref(l)]_   
  
  
c++20 deprecated "top-level comma expression in array subscript". A stackoverflow explains it very  well (https://stackoverflow.com/a/63000331/3952939)  
  
compiler: gcc-10.2.0  
flag: -std=c++2a -Werror  
  
suggested fix: adding parenthese for "comma" expression like following:  
            =   word  [**(**++ref(w), ref(c) += distance(_start, _end)**)**]  
            |   eol   [**(**++ref(c), ++ref(l)**)**]

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-05-13 16:20:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/677#issuecomment-840671175  

Almost duplicate of #652. V2 docs/examples are not on my priority list. PR is welcome.

---

## Comment 2

> Username: saki7  
> Created at: 2025-09-05 06:00:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/677#issuecomment-3257164505  

V2 only supports C++11. Comma expression in C++11 should work. We don't support using V2 with the "-std=c++2a" flag now.
