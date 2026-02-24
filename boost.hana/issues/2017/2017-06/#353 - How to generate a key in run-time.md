# #353 - How to generate a key in run-time? [Closed]

> Username: vincent-hui  
> Created at: 2017-06-14 11:25:49 UTC  
> Updated at: 2017-10-08 18:42:21 UTC  
> Closed at: 2017-06-14 17:42:25 UTC  
> Url: https://github.com/boostorg/hana/issues/353  

Is it possible to generate a key in run-time? If yes, can an example be made as the following?  
  
#include <boost/hana/assert.hpp>  
#include <boost/hana/at_key.hpp>  
#include <boost/hana/integral_constant.hpp>  
#include <boost/hana/map.hpp>  
#include <boost/hana/pair.hpp>  
#include <boost/hana/type.hpp>  
#include <iostream>  
  
#include <string>  
namespace hana = boost::hana;  
  
  
int main() {  
  
  auto my_map = hana::make_map(  
    hana::make_pair(hana::int_c<7>, 7),  
    hana::make_pair(hana::int_c<13>, 13),  
    hana::make_pair(hana::int_c<23>, 23)  
  );  
      
    int key = 7;  
    std::cout << my_map[key] << "\n";  
}

---

## Comment 1

> Username: ldionne  
> Created at: 2017-06-14 17:42:25 UTC  
> Url: https://github.com/boostorg/hana/issues/353#issuecomment-308505961  

No, this is not possible. The reason is that in the general case, the return type of the map would depend on the value of the key given, which is impossible. The only alternative would be to restrict this to work only when all the values have the same type, or return a variant of all the possible types. However, this requires different machinery than what Hana was built for, and hence is not supported.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2017-06-14 17:48:32 UTC  
> Updated at: 2017-10-08 18:42:21 UTC  
> Url: https://github.com/boostorg/hana/issues/353#issuecomment-308507615  

(oops didn't see there was already a response to this)  
  
As stated in this issue https://github.com/boostorg/hana/issues/348, it is out of scope for Boost.Hana to map run-time values to compile-time values. `hana::map`'s keys could indeed have run-time state, but in your case its type would be `int` of which there could be only one since keys are required to be unique.  
  
There appears to be a lot of interest in this lately so I'll post links that may be relevant:  
https://github.com/jacquelinekay/petra  
https://github.com/pdimov/mp11  
https://www.youtube.com/watch?v=hwS0ZvkKuSQ <-- @mcalabrese has a "Call" library that supposedly handles this, but he hasn't posted it to Github yet.

---

## Comment 3

> Username: brunocodutra  
> Created at: 2017-06-15 18:28:25 UTC  
> Url: https://github.com/boostorg/hana/issues/353#issuecomment-308828481  

I'll shamelessly plug Alloy here as well https://github.com/brunocodutra/alloy
