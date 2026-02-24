# #103 - Bjørn's review [Closed]

> Username: HDembinski  
> Created at: 2018-09-24 21:06:32 UTC  
> Updated at: 2018-12-17 15:10:14 UTC  
> Closed at: 2018-12-17 15:10:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/103  

Boost.Histogram contains useful and well-designed features, like excess  
(over/underflow) bins, and axis transforms to name a few.  
  
As this is a review, I am going to spent most time below on critical  
scrutiny.  
  
  
I. DESIGN  
---------  
  
Iterators are const. This prevents us from bootstrapping the histogram  
with a prior distribution using STL algorithms like std::fill(),  
std::generate(), or std::sample().  
  
The adaptive_storage does not work well with STL algorithms, because  
weight_counter is not an arithmetic type. See the Implementation section  
below for more detail. I therefore propose that the default storage  
policy is changed to something without weight_counter.  
  
The adaptive_storage has two responsibilities: data compaction and  
weights. Would it be possible to split this into two separate storage  
policies? I have no real use for arrival variance.  
  
I am not too fond of using operator() for insertion. The code looks like  
a function call with side-effect. I prefer an explicitly named member  
function.  
  
The axis::ouflow_type is oddly named. I suggest that this is renamed to  
something like axis::excess_type.  
  
  
II. IMPLEMENTATION  
------------------  
  
The implementation is generally of high quality. However, I did  
encounter the three problem areas listed below.  
  
(A) Integration with STL algorithms can be improved. Here are some  
examples:  
  
First, std::distance() does not work on a histogram with array_storage.  
This means that other STL algorithms, like std::any_of(), fails to  
compile. I solved this problem by copying the distance_to() function  
from the axis iterator to the histogram iterator.  
  
Second, std::max_element (and brethren) cannot be used on a histogram.  
Consider the following example:  
  
 auto element = std::max_element(h.begin(), h.end());  
  
Compilation fails for adaptive_storage because weight_counter has no  
less-than operator. Furthermore, compilation fails for array_storage  
because iterator_over<H>::operator= fails. It attempts to re-assign  
a reference, but accidentally triggers a copy-constructor instead.  
Letting the iterator store a pointer instead of a reference solves the  
problem.  
  
Apropos, iterator_over<H>::operator= does not return *this. Consider  
adding the -Werror=return-type compiler flag and a unit-test to that  
calls this operator.  
  
Third, std::inner_product fails to compile for adaptive_storage because  
weight_counter does not have a binary operator*. The inner product of  
two histograms is useful for calculating the cosine similarity, which  
can be used to compare two distributions. std::inner_product works for  
array_storage though.  
  
(B) Indexing and size use different types (int versus std::size_t.) I  
assume that this is because of the underflow bin, which is indexed by  
-1. I am not certain whether or not this is a real problem, but it does  
cause some oddities when stressed to the limit. For example, if we need  
a histogram that counts each unsigned int, then we get an "lower <  
upper" exception during construction:  
  
 using range_type = unsigned int;  
 auto h = make_static_histogram_with(  
   array_storage<int>(),  
   axis::integer<>(0, std::numeric_limits<range_type>::max()));  
  
It works if we reduce the end by 2, but then we are not collecting all  
values (unless we shift the range left by 1 and misuse the excess bins  
for the two missing values.)  
  
A possible solution could be to replace the -1 and N excess indices with  
an "enum struct excess { lower, upper }" and let operator[] et al  
use overloading on this enum.  
  
(C) Sometimes the compilation errors are nonsensical. For example:  
  
 // Forgot to use make_static_histogram_with()  
 auto h = make_static_histogram(array_storage<int>,  
                                axis::regular<>(10, 0, 1));  
  
triggers an incomprehensible static_assert plus an error about a missing  
.shape() function.  
  
  
III. DOCUMENTATION  
------------------  
  
User guide is clear and pedagogical.  
  
I would like to see more examples that uses STL algorithms, such as  
calculating the CDF using std::partial_sum(), or calculating the cosine  
similarity of two histograms using std::inner_product().  
  
Most examples use std::cout plus a comment to document the results of  
operations. Consider using assert() instead.  
  
Consider using a tabular layout similar to that of the C++ standard (or  
cppreference.com for that matter) on the Concepts page.  
  
Reference documentation is rather meager and shows implementation  
details.  
  
The documentation contains both a "Reference" and a "References" chapter  
which are completely different. Consider renaming the latter to  
something like "External references" , "Literature references", or  
"Bibliography".  
  
  
IV. MISC  
--------  
  
I have spent around 15 hours on the review, mainly writing small  
examples that uses STL algorithms on Boost.Histogram.  
  
I am well-versed in the topic. I work with statistical distributions  
for real-time analysis of data, although I mainly dabble around in one  
dimension. I have written a library for online/streaming statistical  
algorithms.  
  
  
V. VERDICT  
----------  
  
While Boost.Histogram is a small niche library, it has a wide range of  
practical applications to warrant the inclusion into Boost.  
  
Boost.Histogram should be ACCEPTED into Boost, provided:  
  
 1. Reference documentation is finalized.  
  
I furthermore strongly recommend that the default storage policy is  
changed to something without weight_counter because of the various  
problems with STL algorithms. This recommendation is not a prerequisite  
for acceptance.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-12-17 15:10:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/103#issuecomment-447878509  

Closing this, the remaining open items were converted into individual issues
