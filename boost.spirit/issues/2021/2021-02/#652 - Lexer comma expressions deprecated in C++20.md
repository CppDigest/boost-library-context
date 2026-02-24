# #652 - Lexer comma expressions deprecated in C++20 [Closed]

> Username: wouterbeek  
> Created at: 2021-02-24 23:28:16 UTC  
> Updated at: 2021-02-25 15:14:28 UTC  
> Closed at: 2021-02-25 15:14:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/652  

According to the [Boost documentation](https://www.boost.org/doc/libs/1_75_0/libs/spirit/doc/html/spirit/lex/tutorials/lexer_quickstart2.html) a comma expression can be used in an array subscript in order to tie multiple actions to a simple token match:  
```cpp  
t_line [++boost::phoenix::ref(m_lines),  
        ++boost::phoenix::ref(m_characters)];  
```  
  
Unfortunately, C++20 has deprecated this usage, resulting in warnings (or errors, when warnings-as-errors is enabled) in modern compilers.  The following is an example of such a warning in GCC 10:  
  
```  
{file}.cpp:{line}:{column}: error: top-level comma expression in array subscript is deprecated [-Werror=comma-subscript]  
   {line} |       t_line [++boost::phoenix::ref(m_lines),  
```  
  
What is the best way to solve this issue when creating a C++20 compatible lexer?

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-02-24 23:36:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/652#issuecomment-785464900  

According to http://wg21.link/p1161 you should enclose the expression in parentheses, that's what I did with tests https://github.com/boostorg/spirit/commit/475877a3dae5ed2977261e4b59064fcb45f2fbbb, but have not addressed it in docs/examples.

---

## Comment 2

> Username: wouterbeek  
> Created at: 2021-02-25 15:14:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/652#issuecomment-785976205  

@Kojoley Thanks!  The use of parentheses indeed solves this issue.  It is not Boost Spirit-specific at all, so I'll quickly close this issue.
