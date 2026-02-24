# #55 - warning: unused parameter size_i and size_j [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 08:24:35 UTC  
> Updated at: 2019-03-04 07:06:35 UTC  
> Closed at: 2019-03-04 07:06:35 UTC  
> Url: https://github.com/boostorg/ublas/issues/55  

This program:  
```  
#include <boost/numeric/ublas/functional.hpp>  
int main() {  
  return 0;  
}  
```  
when compiled on linux x86_64 with clang++ 5.0.2 and boost 1.68.0 beta1 with the following command:  
`clang++ -o ublas.o -c -DNDEBUG -Wall -Wextra -std=c++14 -I/softs/lin64-clang-5.0.2/release/boost/include ublas.cpp`  
gives the following warnings:  
```  
In file included from ublas.cpp:1:  
/softs/lin64-clang-5.0.2/release/boost/include/boost/numeric/ublas/functional.hpp:1673:57: warning: unused parameter 'size_i' [-Wunused-parameter]  
        size_type upper_element (size_type i, size_type size_i, size_type j, size_type size_j) {  
                                                        ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/numeric/ublas/functional.hpp:1673:88: warning: unused parameter 'size_j' [-Wunused-parameter]  
        size_type upper_element (size_type i, size_type size_i, size_type j, size_type size_j) {  
                                                                                       ^  
2 warnings generated.  
```
