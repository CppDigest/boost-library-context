# #123 - Compile Error When Testing with MSVC Using /std:c++latest [Open]

> Username: NEIL-smtg  
> Created at: 2025-04-11 08:00:01 UTC  
> Updated at: 2025-04-11 08:32:02 UTC  
> Url: https://github.com/boostorg/phoenix/issues/123  

Hi, I work on Microsoft Visual C++ testing, where we regularly build popular open-source projects, including Boost, with development builds of our compiler and libraries with some particular CL options such as `/std:c++latest`.  
  
Feel free to close this issue if there is no plan of supporting `/std:c++latest` in the future. This change in `/std:c++latest` will be released soon in next VS update.  
``  
While building and testing Boost from source using `/std:c++latest`, I encountered the following error in the `phoenix` test:  
```c++  
libs\phoenix\test\statement\if_tests.cpp(33): error C2676: binary '[': 'const boost::phoenix::if_gen<boost::phoenix::actor<Expr>>' does not define this operator or a conversion to a type acceptable to the predefined operator  
```  
  
This error was encountered in:  
[test\statement\if_tests.cpp](https://github.com/boostorg/phoenix/blob/develop/test/statement/loops_tests.cpp) (Line 33, 48, 55)  
[test\statement\loops_tests.cpp](https://github.com/boostorg/phoenix/blob/develop/test/statement/loops_tests.cpp) (Line 40, 56, 74)  
[test\statement\switch_tests.cpp](https://github.com/boostorg/phoenix/blob/develop/test/statement/switch_tests.cpp) (Line 59, 72)  
[test\statement\exceptions.cpp](https://github.com/boostorg/phoenix/blob/develop/test/statement/exceptions.cpp) (Line 128, 145, 174)  
[test\bll_compatibility\algorithm_test.cpp](https://github.com/boostorg/phoenix/blob/develop/test/bll_compatibility/algorithm_test.cpp) (Line 54)  
  
In C++23, the definition of a subscript-expression was changed so that it now takes an expression-list so now a top-level ',' is a separator. **The fix is to wrap the expression in parentheses**. For example in loops_test.cpp:  
```c++  
while_(arg1--)  
[  
   cout << arg1 << ", ",  
   ++ref(x)  
],  
```  
  
should be changed to:  
```c++  
while_(arg1--)  
[  
   (cout << arg1 << ", ",   // added opening parenthesis  
   ++ref(x))  // added closing parenthesis  
],  
```  
  
Test log:   
[boost_phoenix_test_log.txt.txt](https://github.com/user-attachments/files/19701209/boost_phoenix_test_log.txt.txt)

---

## Comment 1

> Username: djowel  
> Created at: 2025-04-11 08:32:00 UTC  
> Url: https://github.com/boostorg/phoenix/issues/123#issuecomment-2796216895  

Would you propose a PR fix?
