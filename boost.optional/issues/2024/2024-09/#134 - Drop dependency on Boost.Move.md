# #134 - Drop dependency on Boost.Move [Closed]

> Username: akrzemi1  
> Created at: 2024-09-28 06:44:39 UTC  
> Updated at: 2024-10-06 22:34:26 UTC  
> Closed at: 2024-10-06 22:34:26 UTC  
> Url: https://github.com/boostorg/optional/issues/134  

Currently Boost.Optional uses functions `boost::forward` and `boost::move` from Boost.Move to service compilers that provide some form of rvalue references, but not necessarily `std::forward` and `std::move`.  
  
Boost.Optional needs to provide its own definitions of `forward` and `move` that are `constexpr`. And then the dependency on Boost.Move can be dropped.
