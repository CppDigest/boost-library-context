# #178 - Subroutine call backtracking bug [Open]

> Username: Davidebyzero  
> Created at: 2022-08-09 22:24:20 UTC  
> Updated at: 2022-08-09 22:26:26 UTC  
> Url: https://github.com/boostorg/regex/issues/178  

A pared-down example is matching `(12)3` with the regex `((\d|\((?1)\)){2})`. It matches `(12)`, but should match `(12)3`.  
  
PCRE deals with it correctly: https://regex101.com/r/tVAmEt/1  
Perl, Ruby/Onigmo, and the Python library mrab-regex also deal with it properly.  
  
The regex that led to the discovery of this bug was the looped substitution [`s~([*-/]( *(\d++|\((?1)\))){2})(?!\))~($1)~`](https://codegolf.stackexchange.com/questions/250861/add-parentheses-to-polish-notation/250875#250875), which adds parentheses to Polish notation.  
  
I have tested and confirmed this to be happening in the latest version of Notepad++, [v8.4.4](https://notepad-plus-plus.org/downloads/v8.4.4/), which uses Boost as its regex engine.  
  
Sample program demonstrating the bug:  
  
```  
#include <boost/regex.hpp>  
#include <iostream>  
int main()  
{  
    boost::smatch what;  
    if (boost::regex_search(std::string("(12)3"), what, boost::regex(         "((\\d|\\((?1)\\)){2})"        ))) std::cout << what[0] << '\n';  
    if (boost::regex_search(std::string("(12)3"), what, boost::regex(       "((?>\\d|\\((?1)\\)){2})"        ))) std::cout << what[0] << '\n';  
    if (boost::regex_search(std::string("(12)3"), what, boost::regex("((\\d|\\(((\\d|\\((?1)\\)){2})\\)){2})"))) std::cout << what[0] << '\n';  
    return 0;  
}  
```  
  
This should print three identical lines of `(12)3`, but instead prints `(12)` followed by two lines of `(12)3`. The third case demonstrates the regex "extruded" to an extra level of depth, by substituting itself in place of `(?1)`.
