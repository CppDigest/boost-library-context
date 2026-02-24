# #141 - Check and improve STL compliance [Closed]

> Username: HDembinski  
> Created at: 2018-12-17 15:05:09 UTC  
> Updated at: 2019-01-23 23:17:29 UTC  
> Closed at: 2019-01-23 23:17:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/141  

add appropriate tests and exampes  
  
from #103:  
First, std::distance() does not work on a histogram with array_storage.  
This means that other STL algorithms, like std::any_of(), fails to  
compile. I solved this problem by copying the distance_to() function  
from the axis iterator to the histogram iterator.  
  
Second, std::max_element (and brethren) cannot be used on a histogram.  
Consider the following example:  
  
auto element = std::max_element(h.begin(), h.end());  
  
Compilation fails for adaptive_storage because weight_counter has no  
less-than operator. Furthermore, compilation fails for array_storage  
because iterator_over::operator= fails. It attempts to re-assign  
a reference, but accidentally triggers a copy-constructor instead.  
Letting the iterator store a pointer instead of a reference solves the  
problem.  
  
Apropos, iterator_over::operator= does not return *this. Consider  
adding the -Werror=return-type compiler flag and a unit-test to that  
calls this operator.  
  
Third, std::inner_product fails to compile for adaptive_storage because  
weight_counter does not have a binary operator*. The inner product of  
two histograms is useful for calculating the cosine similarity, which  
can be used to compare two distributions. std::inner_product works for  
array_storage though.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-01-23 23:17:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/141#issuecomment-457004239  

All this works now in develop.
