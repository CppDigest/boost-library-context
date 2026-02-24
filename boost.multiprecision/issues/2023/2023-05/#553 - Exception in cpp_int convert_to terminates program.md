# #553 - Exception in cpp_int convert_to terminates program [Closed]

> Username: martindolezal  
> Created at: 2023-05-12 10:32:05 UTC  
> Updated at: 2023-05-21 15:46:17 UTC  
> Closed at: 2023-05-21 15:46:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/553  

See the following code: https://godbolt.org/z/cs6cbjnPr  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
#include <iostream>  
  
int main() {  
    std::string strNumber = "179769313486231590617005494896502488139538923424507473845653439431848569886227202866765261632299351819569917639009010788373365912036255753178371299382143631760131695224907130882552454362167933328609537509415576609030163673758148226168953269623548572115351901405836315903312675793605327103910016259918212890625";  
    boost::multiprecision::cpp_int number(strNumber);  
  
    try {  
        std::cout << number.convert_to<double>() << "\n";  
    } catch (...) {  
        std::cout << "some exception caught" << "\n";  
    }  
      
    std::cout << "finished" << "\n";  
}  
```  
  
`convert_to<double>()` throws exception here, but it terminates the program, cannot be caught. It seems to be due to `noexcept` qualifier somewhere on the way, probably `eval_convert_to` in cpp_int/misc.hpp  
  
The behavior change between boost 1.78 and boost 1.79.
