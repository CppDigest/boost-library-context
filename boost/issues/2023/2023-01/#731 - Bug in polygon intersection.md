# #731 - Bug in polygon intersection? [Open]

> Username: rram84  
> Created at: 2023-01-23 06:10:30 UTC  
> Updated at: 2023-01-23 06:10:30 UTC  
> Url: https://github.com/boostorg/boost/issues/731  

A, B <- simple polygons  
There appears to be a problem in computing $A \cap B$, although I am not sure which specific cases trigger it.  
The example below reproduces the problem:  
  
// boost polygon utilities  
#include <boost/geometry/geometry.hpp>  
  
// boost aliases  
namespace bg  = boost::geometry;  
namespace bgm = bg::model;  
using boost_point_t   = bgm::point<double, 2, bg::cs::cartesian>;  
using boost_polygon_t = bgm::polygon<boost_point_t, false>;  
  
int main()  
{  
  boost_polygon_t p1{{{76.37593982020243, 53.62855965544195},  
	{76.56629943847656, 53.71099853515625},  
	  {76.97709655761719, 54},  
	    {77.41490173339844, 54.58509826660156},  
	      {77, 55},  
		{76.99999312109907, 55.00000687887207},  
		  {76.99866406552184, 55.00133592887243},  
		    {76.09896357657894, 55.90103264257367},  
		      {76.32990264892578, 54},  
			{76.37593982020243, 53.62855965544195}}};  
  bg::correct(p1);  
  
  
  boost_polygon_t p2{{{77.41490173339844, 54.58509826660156},  
	{77, 55},  
	  {76.09089660644531, 55.90909957885742},  
	    {76, 56},  
	      {76.32990264892578, 54},  
		{76.372802734375, 53.62720108032227},  
		  {76.56629943847656, 53.71099853515625},  
		    {76.97709655761719, 54},  
		      {77.41490173339844, 54.58509826660156}}};  
  bg::correct(p2);  
  
  std::vector<boost_polygon_t> intersection{};  
  bg::intersection(p2, p1, intersection);  
        
  // intersection should be non empty, with one connected component  
  assert(intersection.empty()==false);  
  assert(static_cast<int>(intersection.size())==1);  
    
  std::cout << std::endl << std::endl << "Intersection: " << std::endl;  
  for(auto& it:intersection[0].outer())  
    std::cout << bg::get<0>(it) << " " << bg::get<1>(it) << std::endl;  
}  
  
  
  
Output (incorrect):   
76.3759 53.6286  
76.5663 53.711  
76.9771 54  
77.4149 54.5851  
77 55  
77 55  
76.0909 55.9091  
76 56  
76.3299 54  
76.3728 53.6272  
76.5663 53.711  
76.9771 54  
77.4149 54.5851  
77 55  
76.9997 55.0003  
76.9987 55.0013  
76.099 55.901  
76.3299 54  
76.3759 53.6286
