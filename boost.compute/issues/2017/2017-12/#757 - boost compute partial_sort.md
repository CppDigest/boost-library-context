# #757 - boost compute partial_sort [Open]

> Username: WilliamTambellini  
> Created at: 2017-12-21 20:23:18 UTC  
> Updated at: 2018-02-24 11:28:31 UTC  
> Url: https://github.com/boostorg/compute/issues/757  

Would it make sens to implement a boost::compute::partial_sort ?  
Pure Cpp std::partial_sort :   
http://en.cppreference.com/w/cpp/algorithm/partial_sort  
Looping by calling several nth_element() could be a workaround I guess.  
Kind

---

## Comment 1

> Username: kylelutz  
> Created at: 2017-12-22 17:13:56 UTC  
> Url: https://github.com/boostorg/compute/issues/757#issuecomment-353639682  

Yeah, we should be able to provide ` partial_sort()` implementation. Are you interested in submitting a pull request for this?

---

## Comment 2

> Username: WilliamTambellini  
> Created at: 2018-01-04 20:03:25 UTC  
> Updated at: 2018-01-05 19:56:15 UTC  
> Url: https://github.com/boostorg/compute/issues/757#issuecomment-355384430  

Hi @kylelutz   
I dont plan to implement it by myself but could help for testing/benchmarking.   
Would the radix select from viennaCL be good enough :   
https://github.com/vpa1977/smalltests/blob/master/radix_select.hpp  
?  
kind
