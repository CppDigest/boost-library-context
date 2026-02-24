# #139 - extreme value distribution's mean/std fail for location=0 [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 17:53:38 UTC  
> Updated at: 2018-08-19 17:57:17 UTC  
> Closed at: 2018-08-19 17:57:17 UTC  
> Url: https://github.com/boostorg/math/issues/139  

Extreme value distribution's mean and standard_deviation use both detail::check_scale for location parameter a, which seems wrong, as there's no limitation for location parameter a (besides being finite).  
  
This causes even a standard extreme value distribution to fail:  
```  
BOOST_AUTO_TEST_CASE(checkScaleIssueMean)  
{  
	boost::math::extreme_value_distribution<> G;  
  
	boost::math::mean(G);  
}  
  
BOOST_AUTO_TEST_CASE(checkScaleIssueStd)  
{  
	boost::math::extreme_value_distribution<> G;  
  
	boost::math::standard_deviation(G);  
}  
```  
with  
```  
std::domain_error: Error in function boost::math::mean(const extreme_value_distribution<double>&): Scale parameter is 0, but must be > 0 !  
  
std::domain_error: Error in function boost::math::standard_deviation(const extreme_value  
```  
  
Moved from Trac.
