# #284 - Undefined behavior in basic_cstring<CharT>::trim_right [Closed]

> Username: zim-tis  
> Created at: 2020-09-17 12:58:07 UTC  
> Updated at: 2022-03-05 20:38:28 UTC  
> Closed at: 2022-03-05 20:38:28 UTC  
> Url: https://github.com/boostorg/test/issues/284  

The loop:  
```c  
    for( it = end()-1; it != begin()-1; --it ) {  
        if( self_type::traits_type::find( exclusions.begin(),  exclusions.size(), *it ) == reinterpret_cast<pointer>(0) )  
            break;  
    }  
```  
in the function `basic_cstring<CharT>::trim_right` contains an undefined behavior with respect to the C++17 Standard.  
  
The operation `begin()-1` used a stopping condition is undefined, following N4659[expr.add]p4:  
  
> When an expression that has integral type is added to or subtracted from a pointer, the result has the type  
of the pointer operand. If the expression P points to element x[i] of an array object x with n elements, 86  
the expressions P + J and J + P (where J has the value j) point to the (possibly-hypothetical) element  
x[i + j] if 0 ≤ i + j ≤ n; otherwise, the behavior is undefined. Likewise, the expression P - J points to the  
(possibly-hypothetical) element x[i − j] if 0 ≤ i − j ≤ n; otherwise, the behavior is undefined.  
  
The undefined behavior can be fixed by rewriting the loop as:  
```c  
    for( it = end(); it != begin(); ) {  
        --it;  
        if( self_type::traits_type::find( exclusions.begin(),  exclusions.size(), *it ) == reinterpret_cast<pointer>(0) )  
            break;  
    }  
```  
  
This codepath can be hit with the file used introduction example:  
```c  
#define BOOST_TEST_MODULE My Test  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE(first_test)  
{  
  int i = 1;  
  BOOST_TEST(i);  
  BOOST_TEST(i == 2);  
}  
```
