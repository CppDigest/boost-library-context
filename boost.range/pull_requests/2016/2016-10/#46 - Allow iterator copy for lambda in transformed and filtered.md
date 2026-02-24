# #46 Allow iterator copy for lambda in transformed and filtered [Merged]

> Username: jeanphilippeD  
> Created at: 2016-10-23 22:42:46 UTC  
> Updated at: 2017-06-30 11:43:38 UTC  
> Merged at: 2017-06-30 11:43:38 UTC  
> Closed at: 2017-06-30 11:43:38 UTC  
> Url: https://github.com/boostorg/range/pull/46  

Lambda has no default constructor nor a copy or move assignment.  
  
range\test\adaptors.cpp is checking that produced ranges are copiable,  
but it is not doing so the for lambda, or lambda like objects.  
  
default_constructible_unary_fn uses an optional for default  
construction.  
I extended it to use its emplace facility to copy assign non copy  
assignable types.  
(see boost::optional 'Type Requierment':  'If T is not MoveAssignable,  
it is still possible to reset the value of optional<T> using function  
emplace():')  
  
For info,  
I got an implementation of the range-v3 calendar example working on boost::range.  
In order to replace my function object structs with lambda, i needed the iterator to still be copy assignable, with non copy-assignable function objects (I might have been able to work around it, but it seemed fairly reasonable)  
  
The current pull request allowed me to do it:  
https://github.com/jeanphilippeD/SimpleExperiments/commit/4d81ff3133b0bfc2ef85fc7f34acf418dbbf34fb

---
