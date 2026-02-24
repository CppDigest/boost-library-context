# #47 - feat: modifying functions having iterators as input should be overloaded with functions taking const_iterator [Open]

> Username: lano1106  
> Created at: 2023-06-01 21:33:02 UTC  
> Updated at: 2023-06-01 21:33:02 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/47  

This is what most standard containers are doing today.  
  
By replacing std::deque with boost::circular, I got a few compilation errors...  
  
Prior to C++11, the guideline was to prefer iterator because of some STL API defect in regard to const_iterator but this got fixed in C++11  
  
References:  
Effective STL book  
item 26: Prefer iterator to const_iterator, reverse_iterator, and const_reverse_iterator  
  
Effective Modern C++ book  
Item 13: Prefer const_iterators to iterators
