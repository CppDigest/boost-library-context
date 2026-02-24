# #1092 - Cartesian distance fails to compile (This operation is not or not yet implemented) [Open]

> Username: Quasarts  
> Created at: 2022-07-12 13:35:29 UTC  
> Updated at: 2022-11-16 14:03:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/1092  

Whatever geometry is used.   
Boost is compiled with xcode 13.4.1  
  
Example :   
   typedef boost::geometry::model::d2::point_xy<double> point_t;  
   typedef boost::geometry::model::polygon<point_t> poly_t;  
         
  point_t p(ray.m_origin.x, ray.m_origin.y);  
  
 poly_t poly;  
        poly.outer().push_back(point_t(R0.x, R0.z));  
        poly.outer().push_back(point_t(R1.x, R1.z));  
        poly.outer().push_back(point_t(L0.x, L0.z));  
        poly.outer().push_back(point_t(L1.x, L1.z));  
        poly.outer().push_back(point_t(R0.x, R0.z));  
  
        auto distance = boost::geometry::distance(p, poly);  
  
We get :   
Static_assert failed due to requirement 'boost::geometry::detail::static_assert_check<false, boost::geometry::point_tag, boost::geometry::areal_tag>::value' "This operation is not or not yet implemented."

---

## Comment 1

> Username: vissarion  
> Created at: 2022-11-16 14:02:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1092#issuecomment-1317071797  

What version of boost are you using? Could you please share a minimal example so that we can reproduce it? In the current one there are undefined variables and syntactic typos.
