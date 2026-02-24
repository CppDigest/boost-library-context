# #110 - algorithm.cpp failed with clang-cl on Windows for "declaration does not declare anything" [Open]

> Username: yuxianch  
> Created at: 2020-08-19 02:18:43 UTC  
> Updated at: 2020-08-19 02:18:43 UTC  
> Url: https://github.com/boostorg/range/issues/110  

With the latest version of clang-cl, algorithm.cpp get failed with clang-cl on Windows for "declaration does not declare anything".  
Related code:  
https://github.com/boostorg/range/blob/72e2a4457a13eba0b8cddd2e68f058f74bd3d890/test/algorithm.cpp#L32-L35  
A small reproducer(test.cpp):  
```  
namespace boost {  
  template <class T>  
  class counting_iterator  
  {};  
}  
template ::boost::counting_iterator<int>;  
```  
Error message:  
```  
ksh-3.2$ clang-cl -c test.cpp  
test.cpp(6,10): error: declaration does not declare anything  
template ::boost::counting_iterator<int>;  
         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
The code is considered as not accepted(https://github.com/llvm/llvm-project/commit/1cbdf932b41eb58ed94fbc240e93b63653d95bd3).
