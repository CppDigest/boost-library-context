# #368 Fix clang-pch implementation to actually use pch instead of pth. [Merged]

> Username: erichkeane  
> Created at: 2018-11-15 16:41:59 UTC  
> Updated at: 2021-10-02 21:19:29 UTC  
> Merged at: 2018-11-18 20:22:23 UTC  
> Closed at: 2018-11-18 20:22:23 UTC  
> Url: https://github.com/boostorg/build/pull/368  

As listed in issue #367, PTH is/was an undocumented experimental feature of  
clang that is considered 'failed' and is currently being removed from  
the next version of clang.  Additionally, there is a bug in the  
implementation of PTH in clang that results in severe miscompilation of  
code using about 1/3 of the available tokens.  
  
This patch replaces PTH with PCH, which completely subsumes PTH's  
functionality. Additionally, PCH mode on clang should now be faster as a  
result.  
  
For edification: The difference between PTH and PCH is which step the  
compiler stops to save to the binary file.  PTH was intended to execute  
after the preprocessor has executed and commits the token list to the  
binary file.  PCH will go through semantic analysis and commit the AST  
to the binary file.

---
