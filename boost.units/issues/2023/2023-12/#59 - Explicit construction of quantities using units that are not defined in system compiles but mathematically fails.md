# #59 - Explicit construction of quantities using units that are not defined in system compiles but mathematically fails [Open]

> Username: tsaubergine  
> Created at: 2023-12-15 05:34:31 UTC  
> Updated at: 2023-12-15 05:36:02 UTC  
> Url: https://github.com/boostorg/units/issues/59  

If I define a unit in a system that doesn't have this base dimension, e.g. angular_velocity in the degrees system:  
  
```  
quantity<unit<angular_velocity_dimension,degree::system>> angular_vel_deg(30*degree::degrees/si::seconds);  
```  
  
This compiles fine, and explicit conversions also compile fine:  
  
```  
quantity<unit<angular_velocity_dimension, si::system>> angular_vel_si(angular_vel_deg);  
```  
  
However when I go to run it, I get `angular_vel_si` as `30 rad/s` rather than the expected `0.523 rad/s`  
  
The fact that this compiles seems to be a bug to me, but most importantly I'd like to be able to detect this situation (ideally with a static_assert) so that I don't have code that compiles but performs incorrect conversions.  
  
With a few hours digging around I couldn't find a satisfactory way to do this, though I think it might be related to https://github.com/boostorg/units/issues/36 but the proposed fix (https://github.com/boostorg/units/pull/37) didn't fix my issue.  
  
Here's my full minimal test case. Thanks in advance for any help you can provide:  
  
```  
#include "units/include/boost/units/systems/si.hpp"  
#include "units/include/boost/units/systems/angle/degrees.hpp"  
#include <cassert>  
#include <iostream>  
#include <cmath>  
  
  
int main()  
{  
    double eps = std::numeric_limits<double>::epsilon();  
  
      
    using boost::units::unit;  
    using boost::units::quantity;  
    namespace si = boost::units::si;  
    namespace degree = boost::units::degree;  
  
    // angle, works as expected  
    {  
        using boost::units::plane_angle_dimension;  
        quantity<unit<plane_angle_dimension,degree::system>> angle_deg(30*degree::degrees);  
        quantity<unit<plane_angle_dimension,si::system>> angle_si(angle_deg);  
  
        std::cout << "Degrees: " << angle_deg.value() << ", radians: " << angle_si.value() << std::endl;  
  
        assert(std::abs(angle_deg.value()-angle_si.value()) > eps);  
    }  
      
    // angular velocity: fails but more importantly, it compiles (when I feel that it shouldn't)  
    {  
        using boost::units::angular_velocity_dimension;  
  
        quantity<unit<angular_velocity_dimension,degree::system>> angular_vel_deg(30*degree::degrees/si::seconds);  
        quantity<unit<angular_velocity_dimension, si::system>> angular_vel_si(angular_vel_deg);  
  
        std::cout << "Degrees/sec: " << angular_vel_deg.value() << ", radians/sec: " << angular_vel_si.value() << std::endl;  
          
        assert(std::abs(angular_vel_deg.value()-angular_vel_si.value()) > eps);  
    }  
      
}  
```  
  
Output:  
```  
Degrees: 30, radians: 0.523599  
Degrees/sec: 30, radians/sec: 30  
a.out: test2.cpp:38: int main(): Assertion `std::abs(angular_vel_deg.value()-angular_vel_si.value()) > eps' failed.  
zsh: IOT instruction (core dumped)  ./a.out  
```  
  
Expected (or compile failure would also be acceptable):  
```  
Degrees: 30, radians: 0.523599  
Degrees/sec: 30, radians/sec: 0.523599  
```
