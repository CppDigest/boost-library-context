# #639 - Tie-to-even rule is not followed for overload remainder(float128, int) [Open]

> Username: cffk  
> Created at: 2024-10-10 18:52:58 UTC  
> Updated at: 2024-10-10 18:52:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/639  

Running the following code  
```c++  
#include <iostream>  
#include <boost/multiprecision/float128.hpp>  
  
int main() {  
  using std::remainder;  
  boost::multiprecision::float128 x = 180, d = 360;  
  std::cout << remainder(x, 360) << " " << remainder(x, d) << "\n";  
}  
```  
prints out `-180 180`.  I expected both numbers to be the same and the tie-to-even rule for `remainder` dictates that `180` is the correct result.  
  
I'm on a Fedora 40 machine with boost version 18.3.0 installed.
