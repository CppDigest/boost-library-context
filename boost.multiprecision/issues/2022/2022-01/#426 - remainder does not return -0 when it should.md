# #426 - remainder does not return -0 when it should [Closed]

> Username: cffk  
> Created at: 2022-01-31 20:22:22 UTC  
> Updated at: 2022-02-02 09:07:54 UTC  
> Closed at: 2022-02-02 09:07:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/426  

According to https://en.cppreference.com/w/cpp/numeric/math/remainder  
```text  
If the returned value [of remainder] is 0, it will have the same sign as x.  
```  
  
However, the `float128` implementation of `remainder` always returns `+0` as illustrated by  
  
```C++  
#include <iostream>  
#include <boost/multiprecision/float128.hpp>  
  
int main() {  
  using std::remainder; using std::signbit;  
  boost::multiprecision::float128 d = 360.0Q;  
  for (int i = 2; i >= -2; --i) {  
    boost::multiprecision::float128 x = i * d, y = remainder(x, d);  
    std::cout << x << " " << y << " " << signbit(y) << "\n";  
    if (i == 0) {  
      x = -x;  
      y = remainder(x, d);  
      std::cout << x << " " << y << " " << signbit(y) << "\n";  
    }                    
  }  
}  
```  
which prints  
```text  
720 0 0  
360 0 0  
0 0 0  
-0 0 0  
-360 0 0  
-720 0 0  
```  
The last three lines should be  
```text  
-0 -0 1  
-360 -0 1  
-720 -0 1  
```  
  
I'm using boost 1.76.0.
