# #167 - no_tracking flag has no effect? [Closed]

> Username: correaa  
> Created at: 2019-08-17 01:26:42 UTC  
> Updated at: 2019-10-19 16:24:09 UTC  
> Closed at: 2019-10-19 16:24:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/167  

There is a flag called `no_tracking` that can be passed to an `xml_oarchive` constructor (together with `no_header`). However, it doesn't seem to have any effect, is this a bug?  
  
```  
	boost::archive::xml_oarchive oa{std::cout, boost::archive::no_header | boost::archive::no_tracking};   
```  
  
An example output here: https://stackoverflow.com/a/57532638/225186

---

## Comment 1

> Username: correaa  
> Created at: 2019-08-17 02:25:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/167#issuecomment-522196780  

As a matter of fact `track_never` seems to be ignored systematically, for example in this example  
  
```  
#include<boost/archive/xml_oarchive.hpp>  
#include<boost/archive/text_oarchive.hpp>  
#include<boost/units/quantity.hpp>  
  
BOOST_CLASS_TRACKING(boost::units::quantity<boost::units::si::length>, boost::serialization::track_never);  
  
using namespace boost::units;  
  
int main(){  
	boost::units::quantity<boost::units::si::length> q = 12.*boost::units::si::meter;  
	boost::archive::text_oarchive toa{std::cout};   
	toa << q;  
}  
```  
  
I get `22 serialization::archive 17 0 0 1.20000000000000000e+01`  
instead of the expected: `22 serialization::archive 17 1.20000000000000000e+01` (no zeros).  
  
(same thing happens with `xml_oarchive`)  
  
I don't know if this is different bug or it is the same as the other tracking problem.

---

## Comment 2

> Username: robertramey  
> Created at: 2019-10-19 16:24:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/167#issuecomment-544168046  

I posted a response to the SO issue which I believe addresses this - so I'm closing this issue.
