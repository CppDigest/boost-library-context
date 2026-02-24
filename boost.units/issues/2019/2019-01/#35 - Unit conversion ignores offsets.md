# #35 - Unit conversion ignores offsets. [Closed]

> Username: rantanplan42  
> Created at: 2019-01-17 21:26:20 UTC  
> Updated at: 2019-01-19 10:25:25 UTC  
> Closed at: 2019-01-19 10:25:25 UTC  
> Url: https://github.com/boostorg/units/issues/35  

Unit conversions only consider conversion factors. Conversion offsets are ignored. As consequence, all conversions requiring conversion offsets are faulty.  
This affects the conversion from degree celsius to kelvin or farenheit. Also all user defined units that make use of BOOST_UNITS_DEFINE_CONVERSION_OFFSET do not work correctly.  
  
A short example:  
```c++  
#include <iostream>  
  
#include <boost/units/systems/si.hpp>  
#include <boost/units/systems/temperature/celsius.hpp>  
#include <boost/units/systems/temperature/fahrenheit.hpp>  
#include <boost/units/io.hpp>  
  
  
int main(int , char **) {  
    using namespace boost::units;  
  
    quantity<celsius::temperature> tempInCelsius = 5.0 * celsius::degrees;  
    quantity<fahrenheit::temperature> tempInFahrenheit(tempInCelsius);  
    quantity<si::temperature> tempInKelvin(tempInCelsius);  
  
    std::cout << tempInCelsius << std::endl;  
    std::cout << tempInFahrenheit << std::endl;  
    std::cout << tempInKelvin << std::endl;  
  
    return 0;  
}  
```  
  
Output of this short program is:  
```  
5 C  
9 F  
5 K  
```  
  
Correct would be:  
```  
5 C  
41 F  
278.15 K  
```  
  
Best regards

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-01-17 21:36:37 UTC  
> Url: https://github.com/boostorg/units/issues/35#issuecomment-455340868  

AMDG  
  
This behavior is expected.  In order to take offsets  
into account, you need to use `absolute`.  
  
https://www.boost.org/doc/html/boost_units/Examples.html#boost_units.Examples.AbsoluteRelativeTemperatureExample  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: rantanplan42  
> Created at: 2019-01-19 10:25:25 UTC  
> Url: https://github.com/boostorg/units/issues/35#issuecomment-455767896  

How could I miss ...  Never mind.  
  
Thank you for helping me out and sorry for the noise.
