# #36 - is_constructible has unexpected results [Open]

> Username: kaihowl  
> Created at: 2019-02-20 15:45:45 UTC  
> Updated at: 2019-02-20 16:45:43 UTC  
> Url: https://github.com/boostorg/units/issues/36  

```  
#include <boost/units/base_units/metric/hour.hpp>  
#include <boost/units/base_units/metric/liter.hpp>  
#include <boost/units/quantity.hpp>  
  
#include <boost/type_traits/is_constructible.hpp>  
  
using TLiter = boost::units::quantity<boost::units::metric::liter_base_unit::unit_type>;  
using THour = boost::units::quantity<boost::units::metric::hour_base_unit::unit_type>;  
  
static_assert(!boost::is_constructible<TLiter, THour>::value, "Should not be constructible from unrelated units");  
```  
https://godbolt.org/z/W-7Nvg  
  
This fails to compile before it is able to check the static assertion with the following error:  
  
```  
error: no matching function for call to 'conversion_factor(boost::units::unit<boost::units::list<boost::units::dim<boost::units::time_base_dimension, boost::units::static_rational<1l> >, boost::units::dimensionless_type>, boost::units::heterogeneous_system<boost::units::heterogeneous_system_impl<boost::units::list<boost::units::heterogeneous_system_dim<boost::units::scaled_base_unit<boost::units::si::second_base_unit, boost::units::scale<60l, boost::units::static_rational<2l> > >, boost::units::static_rational<1l> >, boost::units::dimensionless_type>, boost::units::list<boost::units::dim<boost::units::time_base_dimension, boost::units::static_rational<1l> >, boost::units::dimensionless_type>, boost::units::dimensionless_type> >, void>, boost::units::unit<boost::units::list<boost::units::dim<boost::units::length_base_dimension, boost::units::static_rational<3l> >, boost::units::dimensionless_type>, boost::units::heterogeneous_system<boost::units::heterogeneous_system_impl<boost::units::list<boost::units::heterogeneous_system_dim<boost::units::metric::liter_base_unit, boost::units::static_rational<1l> >, boost::units::dimensionless_type>, boost::units::list<boost::units::dim<boost::units::length_base_dimension, boost::units::static_rational<3l> >, boost::units::dimensionless_type>, boost::units::dimensionless_type> >, void>)'  
  
         return(destination_type::from_value(static_cast<T2>(source.value() * conversion_factor(Unit1(), Unit2()))));  
```  
  
This has consequences for using boost::units inside a boost::variant as the constructor of the variant cannot reliably detect if the given source operand can be converted to any of the contained storage types of the variant.  
  
In our source code base with a vendored-in copy of Boost, I fixed the issue by adding a check that `conversion_factor` is a defined function for the given source and target units of a quantity. If that is not the case, the constructor for explicit conversions is disabled. This does not work with [an output test case](https://github.com/boostorg/units/blob/develop/test/test_output.cpp#L262) at this point in time.  
  
Furthermore, the current solution only fixes the problem in C++11 and beyond.  
  
Before digging deeper and creating a proper PR, I wanted to know if I am looking at the right approach or if I am off on the wrong foot.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-02-20 16:26:21 UTC  
> Url: https://github.com/boostorg/units/issues/36#issuecomment-465650909  

AMDG  
  
That's not quite good enough.  In quantity, you need  
to check for conversion_helper::convert.  conversion_helper  
is where you'll put the check for conversion_factor.  
The only reason that Boost.Units doesn't do this already  
is that it was written in the days when SFINAE was a lot  
less reliable.  Even what we have was quite finicky on  
VC7.1, VC8, and Sun CC 5.9.  I'm fine with it if it doesn't  
work in C++03.  Although you can make it work by providing  
a fallback ellipsis overload with the sizeof trick, it's  
probably not worth the effort.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: kaihowl  
> Created at: 2019-02-20 16:45:43 UTC  
> Url: https://github.com/boostorg/units/issues/36#issuecomment-465659578  

@swatanabe You are right that makes sense. I came to the same conclusion now. Let's see if I can spin a PR out of this.
