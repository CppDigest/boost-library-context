# #96 - Global variables on multiple lines [Open]

> Username: vinniefalco  
> Created at: 2021-09-22 14:35:48 UTC  
> Updated at: 2021-09-22 14:35:48 UTC  
> Url: https://github.com/boostorg/docca/issues/96  

When a global variable is emitted it is all on one line:  
![image](https://user-images.githubusercontent.com/1503976/134364104-825335d2-5f79-4950-a4f7-949fbfe2863e.png)  
  
it would be better formatted thusly, the same as the functions:  
```  
constexpr  
bnf::lut_chars  
unreserved_chars;  
```
