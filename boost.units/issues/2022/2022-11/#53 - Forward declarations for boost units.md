# #53 - Forward declarations for boost units [Open]

> Username: SegfaultCreator  
> Created at: 2022-11-14 13:09:23 UTC  
> Updated at: 2022-11-14 13:09:23 UTC  
> Url: https://github.com/boostorg/units/issues/53  

Hello dear boost developers,  
  
as i am seeking to reduce the compile times of our code, i am currently trying to reduce heavy includes in header files. For this, i am forward declaring function parameters as in this example:  
  
```  
// Class A.h  
class B;  
  
class A  
{  
   ...  
   void foo(B);  
}  
  
```  
However, i did not find a way to forward declare our type definitions, that rely on boost units such as the definition of a length unit (Length.h) required as a function parameter in the file Object.h:  
  
```  
// Length.h  
#include <boost/units/base_units/si/meter.hpp>  
#include <boost/units/quantity.hpp>  
#include <boost/units/static_constant.hpp>  
  
#include <boost/units/make_scaled_unit.hpp>  
  
using meter_unit = boost::units::si::meter_base_unit::unit_type;  
using Length = boost::units::quantity<meter_unit, double>;  
BOOST_UNITS_STATIC_CONSTANT(Meter, meter_unit);  
BOOST_UNITS_STATIC_CONSTANT(Meters, meter_unit)  
```  
  
```  
// Object.h  
#include <Length.h> // This include shall be avoided  
  
class Position;   
class Object {  
 ...   
  
    bool isNearby(Position pos, Length distance);   
}  
  
```  
  
What I Tried:  
I tried forward declaring the template boost::units::quantity<meter_unit, double>; but i struggeld to fwd-declare the meter_unit that contains both template class meter_base_unit and unit_type inside the namespace of meter_unit.  
  
The meter_base_unit is defined inside boost units as below (simplified) and i guess the unit_type is defined inside the macro BOOST_TYPEOF_REGISTER_TYPE.  
```  
  
// Boost ... Units/Meter.hpp  
namespace boost::units::si {  
    struct meter_base_unit : public base_unit<meter_base_unit, length_dimension, -9>  
    {  
        static std::string name()   { return("meter"); }  
        static std::string symbol() { return("m"); }  
        };  
    }  
}  
  
BOOST_TYPEOF_REGISTER_TYPE(boost::units::si::meter_base_unit)  
```  
  
Is such a constellation even possible to forward declare completely?   
I managed to forward declare the meter unit to some degree as shown here:  
```  
// MyBoostForwardDeclarations/Length_fwd.h  
#include "boost/units/units_fwd.hpp"  
#include <boost/units/base_units/si/meter.hpp>  
  
namespace boost::units  
{  
template<class Unit, class Y> class quantity;  
}  
  
using meter_unit = boost::units::si::meter_base_unit::unit_type;  
using Length = boost::units::quantity<meter_unit, double>;  
```  
  
However, this "forward declaration" header is still an expensive include because it need both _units_fwd.hpp_ and _meter.hpp_  
So therefore, i would prefer to completely forward declare the Length_Unit without including any boost header. Are there any ideas on how i could achieve this?  
  
I am happy for every contribution regarding my specific problem and every contribution leading to deeper understanding of template forward declarations.  
  
Thanks in Advance  
  
SegfaultCreator
