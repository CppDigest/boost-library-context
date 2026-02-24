# #295 - Provide the sqrt function in the Euclidean distance example of the tutorial [Closed]

> Username: juanlucasrey  
> Created at: 2016-07-19 10:52:04 UTC  
> Updated at: 2016-07-23 21:16:07 UTC  
> Closed at: 2016-07-23 21:16:07 UTC  
> Url: https://github.com/boostorg/hana/issues/295  

fully working code to show every example in action (this does not run by the way). This would make the tutorial page easier for the non-initiated in TMP.  
  
``` cpp  
#include <boost/hana.hpp>  
namespace hana = boost::hana;  
using namespace hana::literals;  
using namespace std;  
  
class point {  
public:  
    point(double xin,double yin);  
  
    double x;  
    double y;  
};  
  
point::point(double xin,double yin)  
:x(xin),y(yin)  
{;}  
  
  
template <typename P1, typename P2>  
constexpr auto distance(P1 p1, P2 p2) {  
    auto xs = p1.x - p2.x;  
    auto ys = p1.y - p2.y;  
    return sqrt(xs*xs + ys*ys);  
}  
  
int main() {  
  
    BOOST_HANA_CONSTANT_CHECK(distance(point(3_c, 5_c), point(7_c, 2_c)) == 5_c);  
  
    auto p1 = point(3, 5); // dynamic values now  
    auto p2 = point(7, 2); //  
    BOOST_HANA_RUNTIME_CHECK(distance(p1, p2) == 5); // same function works!  
  
    return 0;  
}  
```

---

## Comment 1

> Username: juanlucasrey  
> Created at: 2016-07-20 10:16:13 UTC  
> Url: https://github.com/boostorg/hana/issues/295#issuecomment-233909766  

actually it looks like you have actually done it!   
  
https://github.com/boostorg/hana/blob/master/example/tutorial/integral.cpp  
  
maybe provide a link to that file for the next readers :)  
  
thanks

---

## Comment 2

> Username: ldionne  
> Created at: 2016-07-20 14:57:16 UTC  
> Url: https://github.com/boostorg/hana/issues/295#issuecomment-233975277  

Right; I thought you wanted to have it as part of the actual tutorial (which I wasn't sure of, because it's really a detail orthogonal to what we're trying to explain).
