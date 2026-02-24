# #902 - Potentially return type mismatch in tanh_sinh::integrate [Closed]

> Username: matwey  
> Created at: 2022-12-15 09:06:38 UTC  
> Updated at: 2022-12-16 09:10:13 UTC  
> Closed at: 2022-12-16 09:10:13 UTC  
> Url: https://github.com/boostorg/math/issues/902  

Hello,  
  
`tanh_sinh::integrate()` has return type of `decltype() const`, where `decltype()` is usually some floating point type returned by value.   
  
https://github.com/boostorg/math/blob/81b7477cefdf879121f5c1ae8c60276bccd0569f/include/boost/math/quadrature/tanh_sinh.hpp#L47  
  
This has no much sense comparing to return just `decltype()`. Didn't you mean other function signature instead?  
```  
const -> decltype()  
```  
  
Making the function itself to be const instead?

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-12-15 15:32:12 UTC  
> Url: https://github.com/boostorg/math/issues/902#issuecomment-1353275152  

That `const` marks the member function as const . . .

---

## Comment 2

> Username: matwey  
> Created at: 2022-12-15 15:50:00 UTC  
> Updated at: 2022-12-15 15:58:13 UTC  
> Url: https://github.com/boostorg/math/issues/902#issuecomment-1353302064  

No, it doesn't.  
  
https://en.cppreference.com/w/cpp/language/function  
  
const qualification is placed before `->`. Everything placed after `->` is trailing return type.  
  
  
Also, please look at the following example:  
  
https://wandbox.org/permlink/IxpAmnxyDEnQExZZ  
  
```  
#include <boost/math/quadrature/tanh_sinh.hpp>  
  
using boost::math::quadrature::tanh_sinh;  
  
class my_x {  
public:  
    float get_value() const {  
        return integrator_.integrate([](float x) {  
            return 1.0f;  
        });  
    }  
private:  
    tanh_sinh<float> integrator_;  
};  
  
int main() {  
      
    return 0;  
}  
```  
  
It fails to compile because `tanh_sinh<float>::integrate()` is NOT cons-qualified function.  
  
Update: Sorry, I've over-engineered the example.  
  
https://wandbox.org/permlink/dyCtBVcosCcW71uC  
  
Here is the simpler one:  
```  
#include <boost/math/quadrature/tanh_sinh.hpp>  
  
using boost::math::quadrature::tanh_sinh;  
  
int main() {  
    const tanh_sinh<float> integrator;  
    integrator.integrate([](float x) {  
        return 1.0f;  
    });  
      
    return 0;  
}  
```

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-12-15 16:23:13 UTC  
> Url: https://github.com/boostorg/math/issues/902#issuecomment-1353351256  

The OP is correct, and there are a few of these.... I'll post a PR shortly.
