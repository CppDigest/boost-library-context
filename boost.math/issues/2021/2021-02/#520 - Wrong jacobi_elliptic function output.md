# #520 - Wrong jacobi_elliptic function output [Closed]

> Username: k2masterpiece  
> Created at: 2021-02-05 11:26:58 UTC  
> Updated at: 2021-02-05 13:10:17 UTC  
> Closed at: 2021-02-05 13:10:16 UTC  
> Url: https://github.com/boostorg/math/issues/520  

```  
#include <iostream>  
#include <boost/math/special_functions/jacobi_elliptic.hpp>  
#include <boost/math/complex.hpp>  
using namespace std;  
int main() {  
    double r = boost::math::jacobi_cn(2, 4);  
    cout << r << endl;  
    return 0;  
}  
```  
The output is 0.885089, but if I use WolframAlpha `jacobiCN(2,4)` = 0.9569963193 , if I use the other eliptic function I am getting wrong output as well  
  
  
I cannot use complex numbers as well:  
  
```  
int main() {  
    complex<double> f(2, 1);  
    complex<double> s(2, 4);  
    complex<double> r = boost::math::jacobi_cn(f, s);  
    cout << r << endl;  
    return 0;  
}  
```  
  
I am getting errors like:  
```  
C:\boost_1_75_0\boost\math\special_functions\jacobi_elliptic.hpp:42: error: no match for 'operator<' (operand types are 'const std::complex<double>' and 'int')  
    if(k < 0)  
       ~~^~~  
C:\boost_1_75_0\boost\math\special_functions\jacobi_elliptic.hpp:48: error: no match for 'operator>' (operand types are 'const std::complex<double>' and 'int')  
    if(k > 1)  
       ~~^~~  
  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-02-05 13:10:16 UTC  
> Url: https://github.com/boostorg/math/issues/520#issuecomment-774024064  

You have the arguments in the wrong order, please refer to https://www.boost.org/doc/libs/1_75_0/libs/math/doc/html/math_toolkit/jacobi/jacobi_elliptic.html and pay particular attention to the bit marked "CAUTION", as not only are the arguments reversed, but rather than the parameter `m` we use the elliptic modulus `k` with `m = k^2`, so the Boost equivalent to `jacobiCN(u, m)`, is `jacobi_cn(sqrt(m), u)`.  
  
We do not currently support complex arguments, submissions in that field are of course welcome ;)
