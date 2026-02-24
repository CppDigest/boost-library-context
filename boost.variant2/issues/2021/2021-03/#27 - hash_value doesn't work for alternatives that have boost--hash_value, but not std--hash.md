# #27 - hash_value doesn't work for alternatives that have boost::hash_value, but not std::hash [Closed]

> Username: pdimov  
> Created at: 2021-03-12 23:30:45 UTC  
> Updated at: 2021-03-13 05:40:02 UTC  
> Closed at: 2021-03-13 05:40:02 UTC  
> Url: https://github.com/boostorg/variant2/issues/27  

E.g. https://godbolt.org/z/s4b33c  
  
This results in `boost::hash_value` working better for `std::variant` than for `boost::variant2::variant`.
