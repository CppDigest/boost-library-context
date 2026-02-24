# #34 - std::iterator is deprecated in C++17 [Closed]

> Username: OlafvdSpek  
> Created at: 2017-10-12 09:33:07 UTC  
> Updated at: 2017-10-15 08:35:04 UTC  
> Closed at: 2017-10-15 08:34:13 UTC  
> Url: https://github.com/boostorg/utility/issues/34  

1>Unknown compiler version - please run the configure tests and report the results  
1>c:\vc\vcpkg\installed\x86-windows\include\boost\operators.hpp(835): error C4996: 'std::iterator<std::input_iterator_tag,V,D,P,R>': warning STL4015: The std::iterator class template (used as a base class to provide typedefs) is deprecated in C++17. (The <iterator> header is NOT deprecated.) The C++ Standard has never required user-defined iterators to derive from std::iterator. To fix this warning, stop deriving from std::iterator and start providing publicly accessible typedefs named iterator_category, value_type, difference_type, pointer, and reference. Note that value_type is required to be non-const, even for constant iterators. You can define _SILENCE_CXX17_ITERATOR_BASE_CLASS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
  
MSVC 15.5.0 Preview

---

## Comment 1

> Username: d-frey  
> Created at: 2017-10-15 08:35:04 UTC  
> Url: https://github.com/boostorg/utility/issues/34#issuecomment-336695253  

I replaces `std::iterator` with a manually rolled version. Please check if it fixes your problem.
