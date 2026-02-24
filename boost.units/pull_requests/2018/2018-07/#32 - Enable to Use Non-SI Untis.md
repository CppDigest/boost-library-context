# #32 Enable to Use Non-SI Untis [Open]

> Username: e-kwsm  
> Created at: 2018-07-23 12:04:42 UTC  
> Updated at: 2021-06-10 02:34:20 UTC  
> Url: https://github.com/boostorg/units/pull/32  

This PR uses https://github.com/boostorg/units/blob/d67f8e7a50ee013d2e71abde1d1eb058f0036812/example/systems.cpp#L25-L34  
  
This PR enables something like  
```cpp  
boost::units::quantity<boost::units::si::pressure>  
    p{760.0 * boost::units::metric::mmHg};  
```

---

## Comment 1

> Username: e-kwsm  
> Created_at: 2018-07-23 12:10:54 UTC  
> Url: https://github.com/boostorg/units/pull/32#issuecomment-407036514  

<details>  
<summary>list</summary>  
<ul>  
<li>angle::arcminute</li>  
<li>angle::arcsecond</li>  
<li>angle::degree</li>  
<li>angle::gradian</li>  
<li>angle::radian</li>  
<li>angle::revolution</li>  
<li>angle::steradian</li>  
<li>astronomical::astronomical_unit</li>  
<li>astronomical::light_day</li>  
<li>astronomical::light_hour</li>  
<li>astronomical::light_minute</li>  
<li>astronomical::light_second</li>  
<li>astronomical::light_year</li>  
<li>astronomical::parsec</li>  
<li>imperial::drachm</li>  
<li>imperial::fluid_ounce</li>  
<li>imperial::foot</li>  
<li>imperial::furlong</li>  
<li>imperial::gallon</li>  
<li>imperial::gill</li>  
<li>imperial::grain</li>  
<li>imperial::hundredweight</li>  
<li>imperial::inch</li>  
<li>imperial::league</li>  
<li>imperial::mile</li>  
<li>imperial::ounce</li>  
<li>imperial::pint</li>  
<li>imperial::pound</li>  
<li>imperial::quart</li>  
<li>imperial::quarter</li>  
<li>imperial::stone</li>  
<li>imperial::thou</li>  
<li>imperial::ton</li>  
<li>imperial::yard</li>  
<li>metric::angstrom</li>  
<li>metric::are</li>  
<li>metric::atmosphere</li>  
<li>metric::bar</li>  
<li>metric::barn</li>  
<li>metric::day</li>  
<li>metric::fermi</li>  
<li>metric::hectare</li>  
<li>metric::hour</li>  
<li>metric::knot</li>  
<li>metric::liter</li>  
<li>metric::micron</li>  
<li>metric::minute</li>  
<li>metric::mmHg</li>  
<li>metric::nautical_mile</li>  
<li>metric::ton</li>  
<li>metric::torr</li>  
<li>metric::year</li>  
<li>us::cup</li>  
<li>us::dram</li>  
<li>us::fluid_dram</li>  
<li>us::fluid_ounce</li>  
<li>us::foot</li>  
<li>us::gallon</li>  
<li>us::gill</li>  
<li>us::grain</li>  
<li>us::hundredweight</li>  
<li>us::inch</li>  
<li>us::mil</li>  
<li>us::mile</li>  
<li>us::minim</li>  
<li>us::ounce</li>  
<li>us::pint</li>  
<li>us::pound</li>  
<li>us::pound_force</li>  
<li>us::quart</li>  
<li>us::tablespoon</li>  
<li>us::teaspoon</li>  
<li>us::ton</li>  
<li>us::yard</li>  
</ul>  
</details>

---
