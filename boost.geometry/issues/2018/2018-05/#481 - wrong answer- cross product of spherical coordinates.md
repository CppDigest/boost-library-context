# #481 - wrong answer: cross product of spherical coordinates [Closed]

> Username: lpranam  
> Created at: 2018-05-10 19:02:21 UTC  
> Updated at: 2018-11-15 02:29:56 UTC  
> Closed at: 2018-11-15 02:29:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/481  

typedef boost::geometry::model::point<double, 3, boost::geometry::cs::spherical<boost::geometry::degree>> spherical_point_3d;  
    typedef boost::geometry::model::point<double, 3, boost::geometry::cs::cartesian> cartesian_point;  
  
    cartesian_point c1(1, 2, 3), c2(1, 2, 7), c3;  
    spherical_point_3d s1, s2, s3, s4;  
  
    boost::geometry::transform(c1, s1);  
    boost::geometry::transform(c2, s2);  
  
    c3 = boost::geometry::cross_product<cartesian_point>(c1, c2);  
    s3 = boost::geometry::cross_product<spherical_point_3d>(s1, s2);  
  
    boost::geometry::transform(c3, s4);  
    cout << "spherical crossproduct:" << boost::geometry::dsv(s3) << endl;  
    cout << "Spherical converted from cartesian:" << boost::geometry::dsv(s4) << endl;  
  
I was trying to do the cross product of two spherical coordinates.  
  
Here I created two cartesian points and then converted both of them into spherical coordinates.  
Then I did cross product of both cartesian points (c1Xc2) and both spherical coordinates (s1Xs2)  
  
answer of (s1Xs2) is wrong. I also checked by converting answer back to cartesian expecting the same answer as (c1Xc2) because spherical coordinates were derived from cartesian coordinates only.  
  
I am new to boost.geometry so if I am doing anything wrong in the code then sorry for creating noise.

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-06-21 12:50:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/481#issuecomment-399092021  

Hi and welcome!  
  
`cross_product()` doesn't reckognize the coordinate system. It compiles for any point and assumes that this point is cartesian for flexibility (in case developer wanted to temporarily store cartesian coordinates in non-cartesian point). So in your case it treats whatever you have in spherical point as cartesian.  
  
`transform()` converts between coordinate systems. In this case my guess is that it does this:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/strategy_transform.hpp#L263  
so converts 3d spherical polar to 3d cartesian ECEF. This means that you have different coordinates in both points and that `cross_product()` should return different results.  
  
Is there something specific you want to calculate?
