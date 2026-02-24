# #72 - BOOST_STL_INTERFACES_USE_DEDUCED_THIS is erroneously set on Clang19 when C++ Standard is 20. [Closed]

> Username: rainerdeyke  
> Created at: 2024-11-10 12:48:44 UTC  
> Updated at: 2024-11-10 18:47:15 UTC  
> Closed at: 2024-11-10 18:47:15 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/72  

BOOST_STL_INTERFACES_USE_DEDUCED_THIS is unilaterally set to 1 on Clang19 so long as BOOST_STL_INTERFACES_USE_CONCEPTS is set to 1, even when using the C++20 standard.  This obviously breaks builds since deduced this is a C++23 feature.
