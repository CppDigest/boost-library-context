# #542 - Direct Formula returns wrong results [Closed]

> Username: barendgehrels  
> Created at: 2018-12-16 18:14:34 UTC  
> Updated at: 2019-07-01 15:10:16 UTC  
> Closed at: 2019-07-01 15:10:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/542  

Hi @vissarion , @awulkiew ,  
  
When I, for buffer, try to calculate the coordinates of a point, at 180 degrees, there is an error.  
  
See this minimal reproduction scenario:  
```  
#include <boost/geometry.hpp>  
  
int main(int, char* [])  
{  
    namespace bg = boost::geometry;  
    typedef bg::strategy::andoyer::template direct  
        <  
            double, true, false, false, false  
        > direct_t;  
  
    bg::srs::spheroid<double> spheroid;  
  
    typedef bg::model::point<double, 2, bg::cs::geographic<bg::degree> > point_type;  
  
    point_type point;  
    bg::set<0>(point, 4.9);  
    bg::set<1>(point, 52.4);  
  
    int const count = 12;  
    double const distance = 1.0;  
    double const two_pi = bg::math::two_pi<double>();  
    double const diff = two_pi / double(count);  
    double angle = 0;  
    for (std::size_t i = 0; i < count; i++, angle += diff)  
    {  
        typename direct_t::result_type dir_r  
                = direct_t::apply(bg::get_as_radian<0>(point),  
                                  bg::get_as_radian<1>(point),  
                                  distance, angle, spheroid);  
        point_type p;  
        bg::set_from_radian<0>(p, dir_r.lon2);  
        bg::set_from_radian<1>(p, dir_r.lat2);  
  
        double const pi = bg::math::pi<double>();  
        std::cout << std::setprecision(20) << " " << angle * 180.0 / pi  
                  << " " << bg::get<0>(p) << " " << bg::get<1>(p) << std::endl;  
    }  
    return 0;  
}  
```  
  
Delivering:  
```  
 0 4.9000000000000003553 52.400008986761207552  
 29.999999999999996447 4.9000073460059256902 52.400007782763282194  
 59.999999999999992895 4.9000127236545552023 52.400004493379917392  
 90 4.9000146920092788605 52.399999999999081979  
 119.99999999999998579 4.9000127236519706031 52.399995506618694208  
 149.99999999999997158 4.9000073460033402029 52.399992217236267322  
 179.99999999999997158 4.9000000000000003553 58.883125680961242665  
 209.99999999999997158 4.8999926539966605077 52.399992217236260217  
 239.99999999999997158 4.8999872763480309956 52.399995506618694208  
 270 4.89998530799072185 52.399999999999081979  
 300.00000000000005684 4.8999872763454428437 52.400004493379931603  
 330.00000000000005684 4.8999926539940723558 52.400007782763275088  
```  
  
You notice that at 179.999 the latitude coordinate is quite off.  
  
I'm using andoyer, I did not try other systems yet.  
  
Note that this is not the reason for the failing area formule, just reported in another issue. Because I'm now starting at 0.0001 to avoid coordinate 180.000 - the generation is then correct. But this is a workaround.

---

## Comment 1

> Username: vissarion  
> Created at: 2018-12-17 11:25:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/542#issuecomment-447812592  

Hi @barendgehrels, this is a known issue of `thomas` formula. See comment in https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/thomas_direct.hpp#L90   
  
That is, this is a "feature" of the formula since for azimuth values close to `pi` the `sin()` and `cos()` evaluations here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/thomas_direct.hpp#L102 are taking almost `0` and `1` values that results to an accuracy in the final result.   
  
I see a few different directions here: (i) try to come up with some formula for that special case (I do not know whether we can come up with something as efficient as it is know though), (ii) use `vincenty` for that case that seems to work, (iii) leave it as is and notify the user for the loss of accuracy (we have other cases where formulas result in inaccurate results see https://github.com/boostorg/geometry/issues/426 https://github.com/boostorg/geometry/issues/449)    
  
**Note:** for direct there is no andoyer formula but we get a 1st order approximation from a special case of `thomas` formula. In the future we have to rename all those formulas using names that include the order of approximation i.e. 1st, 2nd order and "iterative" for `vincenty`; hence remove names of authors Andoyer, Thomas, Vincenty that create confusion.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2018-12-17 19:41:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/542#issuecomment-447972471  

Hi @vissarion thanks for your answer and the backgrounds.  
  
I see the problem. But for users of the formula, like I'm now, this can cause quite a problem.  
  
For the buffer around a point I can work around it. Because if I start the buffer at 0 degree, which I did, I get a at 180 degree (if there is an even number of points). But I can also start at 0.01 degree and then the chance is quite low. However, as soon as the buffer implementation extends to linestrings/polygons, or the number of points in a corner or around a point becomes very high, then the chance of such a 180 degree point is of course high.  
  
I would advocate for (i), a specific handling for that case. I can think of something which is indeed not so efficient: 2 or 5 times slower: calculate two points around (so with pi - offset and pi + offset) and average the points to get the result ("a")  So that is twice as slow, but not accurate enough. Then you can also calculate three other points: 0 - offset, 0, o + offset and calculate the northwards displacement of the 0 point (probably two other is enough). That displacement can be subtracted from the previous result ("a") to get a more accurate result ("b").  
  
So this most probably gives a satisfactory result (if you want I can try it). And because it is an exception, and "only" 4 times slower (Vincenty is perhaps slower than that, but I don't know that), it is probably acceptable.  
  
IMO option (ii) is not so nice, using a completely other formula makes strategies intertwined, and I can imagine that the result is maybe different, so you will get a kind of discontinuity there.  
  
Option (iii) is also not so nice because people will just consider it as a bug, and probably don't understand how to work with a shortcoming of the underlying algorithm.

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-12-17 20:55:30 UTC  
> Updated at: 2018-12-17 20:56:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/542#issuecomment-447995533  

One simple workaround for this behavior could be to normalize the input to always use |azimuth| <= 90 deg in formula. So if |azimuth| > 90 switch latitudes/hemispheres and use either opposite azimuth or azimuth symmetrical wrt equator, calculate for the altered azimuth and then switch back the result accordingly.  
  
And to be clear, what I'm saying is: this is how it could be fixed in the formula.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2018-12-17 21:06:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/542#issuecomment-447998761  

That sounds a lot simpler than the workaround I described above. Yes, let's do that!

---

## Comment 5

> Username: awulkiew  
> Created at: 2019-03-12 03:49:14 UTC  
> Updated at: 2019-03-12 14:23:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/542#issuecomment-471846032  

The switching of latitudes was already done (see: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/thomas_direct.hpp#L94). The problem is that you're passing azimuth in range [0, 360] but valid range is [-180, 180]. This is also the range of azimuth returned from inverse formulas. There is also a utility for normalization of azimuth:  
  
    bg::math::normalize_azimuth<bg::radian>(angle);  
  
which can be called before before passing the azimuth to formulas.  
  
I could put an assertion there but technically this is not something worthy of termination of the program, only inaccuracy. Another solution is to normalize azimuth automatically inside formula. `karney_direct` already does that so we could simply do this in all formulas. What do you think?  
  
EDIT: on the other hand formulas are for internal use and there is no need to normalize inputs that should be correct.

---

## Comment 6

> Username: vissarion  
> Created at: 2019-03-13 09:22:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/542#issuecomment-472342655  

I lean towards putting an assertion in formulas and do not add more complexity by normalization. The caller of the formulas should take care of the correctness of the formula input.

---

## Comment 7

> Username: awulkiew  
> Created at: 2019-03-27 17:05:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/542#issuecomment-477257440  

I proposed a fix for this in PR https://github.com/boostorg/geometry/pull/572. I decided to not normalize automatically but to check assertion. I modified geographic buffer point strategy to pass correct azimuth.  
  
The problem with starting from 0 still remains i.e. very inaccurate area is calculated but AFAIU this is not caused by the direct formula.

---

## Comment 8

> Username: awulkiew  
> Created at: 2019-07-01 15:10:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/542#issuecomment-507305475  

The fix for this issue was merged so I'm closing.
