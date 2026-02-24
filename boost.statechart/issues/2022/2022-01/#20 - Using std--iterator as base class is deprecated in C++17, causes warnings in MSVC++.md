# #20 - Using std::iterator as base class is deprecated in C++17, causes warnings in MSVC++ [Open]

> Username: vbaderks  
> Created at: 2022-01-29 18:55:35 UTC  
> Updated at: 2022-01-29 18:55:35 UTC  
> Url: https://github.com/boostorg/statechart/issues/20  

The following code requires an explicit macro to suppress a warning to be defined when compiling with MSVC++:  
  
https://github.com/boostorg/statechart/blob/586445b824c5cf0e7e6ce4ff2df620fda5d0f0d7/include/boost/statechart/state_machine.hpp#L359  
  
Adding _SILENCE_CXX17_ITERATOR_BASE_CLASS_DEPRECATION_WARNING solves the problem as a workaround. Other Boost libraries have fixed this issue in the past, for example: https://github.com/boostorg/utility/issues/34  
  
Can provide PR if needed.  
  
Background info:  
https://www.fluentcpp.com/2018/05/08/std-iterator-deprecated/
