# #5 Add an "inplace_assign" functor to boost::icl::interval_map [Closed]

> Username: robin-zimmeck  
> Created at: 2015-05-22 12:40:19 UTC  
> Updated at: 2021-04-11 19:28:09 UTC  
> Closed at: 2021-04-11 19:28:09 UTC  
> Url: https://github.com/boostorg/icl/pull/5  

When adding to the container, the functor simply overwrites all hitherto values in the adding interval.

---

## Comment 1

> Username: jofaber  
> Created_at: 2021-03-08 18:30:59 UTC  
> Url: https://github.com/boostorg/icl/pull/5#issuecomment-792975898  

The functors that are provided by the icl library are intended to provide (or preserve) rather ambitious semantic properties described in the documentation e.g.  
https://www.boost.org/doc/libs/1_75_0/libs/icl/doc/html/boost_icl/semantics/collectors__maps_of_sets.html and https://www.boost.org/doc/libs/1_75_0/libs/icl/doc/html/boost_icl/semantics/quantifiers__maps_of_numbers.html  
The laws declared in those chapters to be valid have been examined for the predefined functors.  
  
If users add more functors, we do not know, if the intended semantic properties are valid for them. Users are of  
course free to add behavior by adding own functors. They need to check the resulting behavior themselves in this case.  
  
I am reluctant to add those to the library since I am not sure if the declared laws may be violated.  
As a simple alternative users may apply the functions `set` and `set_at` see:  
https://www.boost.org/doc/libs/1_75_0/libs/icl/doc/html/boost_icl/function_reference/insertion.html#boost_icl.function_reference.insertion.synopsis.setting_values

---
