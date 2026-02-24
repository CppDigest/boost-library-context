# #83 - safe<int> + boost::units [Closed]

> Username: GregKon  
> Created at: 2020-05-07 08:22:23 UTC  
> Updated at: 2020-05-28 14:09:34 UTC  
> Closed at: 2020-05-28 14:09:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/83  

Dear  
  
I have issue with "drop replacement" of integers in boost::units.  
I think issue is connected rather with boost::units than safe, however if you may look into it we will be sure, it is ready to use example to compile.  
It is problem with implicitly cast one scaled unit to another scaling.  
  
  
  
 //**************************************************  
#include "boost/safe_numerics/safe_integer.hpp"  
#include "boost/units/make_scaled_unit.hpp"  
#include "boost/units/quantity.hpp"  
#include "boost/units/systems/si/power.hpp"  
#include "cassert"  
#include "iostream"  
  
using namespace boost::units;  
using namespace boost::units::si;  
using namespace std;  
  
using int_t = int;  
//using int_t = boost::safe_numerics::safe < int >;  //<- this will not work  
  
typedef make_scaled_unit<si::power, scale<10, static_rational<-3>>>::type  
    TagMilliWat;  
  
int main() {  
  quantity<power, int_t> wat_1{1 * watt};  
  quantity<TagMilliWat, int_t> milli_wat{wat_1};  
  
  assert(wat_1.value() == 1);  
  assert(milli_wat.value() == 1000);  
  
  cout << "success\n";  
  return 0;  
}  
 //**************************************************  
  
when using:  
using int_t = boost::safe_numerics::safe<int>;    
than compilation fails due some problem with casting, I think units try to cast by (double) even thou integers are used.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-05-07 14:20:40 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/83#issuecomment-625285002  

very interesting example.  Of course safe numerics would be a very good compliment to boost.units.  Especially if/when safe numerics can be enhanced to include float/double, etc.

---

## Comment 2

> Username: GregKon  
> Created at: 2020-05-07 14:29:53 UTC  
> Updated at: 2020-05-07 14:32:00 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/83#issuecomment-625290360  

do you catch my point? boost.units already support integers.  
(there is problem in formating in my previous post and < int > disapear)  
  
if you use:  
`using int_t = boost::safe_numerics::safe<int>; //<- this will not work`  
  
insted of  
`using int_t = int;`  
  
than compilation fails.

---

## Comment 3

> Username: robertramey  
> Created at: 2020-05-07 14:51:19 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/83#issuecomment-625303305  

I do get your point.  
  
My  point is that if/when safe numerics supports float/double... safe numerics + units will be a killer combination for program correctness.  I honestly believe it would make a huge contribution to safety critical systems.  This had not occurred to me until you brought it up.  
  
Note if you want to isolate the things that Boost units should change  in order to be compatible with safe numerics the maintainer of units might be receptive to it.

---

## Comment 4

> Username: GregKon  
> Created at: 2020-05-08 20:25:18 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/83#issuecomment-626003893  

Indirect reason for compile error is that safe<int> is not convertible to double: no such operator.  
  
However, if architecture of boost.units is that implicitly convertible scaled units is by "double" even thou I use type integer (quantity<power, int_t>), than I have doubts if I want to use it.   
I have run away from floating point for some reasons.

---

## Comment 5

> Username: robertramey  
> Created at: 2020-05-08 22:54:39 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/83#issuecomment-626053835  

so it looks like safe numerics is doing exactly what it is intended to do!!!

---

## Comment 6

> Username: GregKon  
> Created at: 2020-05-28 14:09:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/83#issuecomment-635373627  

Hi  
  
Indeed it make good job, save me from unintentional cast integer to double and again to integer.  
In general in implicit conversion boost::units do cast to double what is very unintentional, specially if you abandon floating point arithmetic. If you specialized quantity<> as float, than you have float -> double- >float. if you specialized long double, than long double -> double -> long double.  
  
However it is only single case, and I have overcome it by specialized conversion_helper<> in units/detail/conversion_impl.hpp for integers, and works (without break regression tests),   
units + safe works.
