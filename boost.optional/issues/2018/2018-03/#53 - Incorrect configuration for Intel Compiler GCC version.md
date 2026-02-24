# #53 - Incorrect configuration for Intel Compiler GCC version [Closed]

> Username: patrick-stephens  
> Created at: 2018-03-28 14:42:59 UTC  
> Updated at: 2022-01-25 08:20:27 UTC  
> Closed at: 2022-01-25 08:20:27 UTC  
> Url: https://github.com/boostorg/optional/issues/53  

https://github.com/boostorg/optional/blob/5182f7f30fad87023ae5e5a4e1f660fb513fb469/include/boost/optional/detail/optional_config.hpp#L125  
  
BOOST_GCC_VERSION is not defined when using the Intel compiler but instead BOOST_INTEL_GCC_VERSION should also be checked for the workaround.  
  
If the Intel GCC version is too old then we get compilation failures as BOOST_OPTIONAL_DETAIL_NO_DEFAULTED_MOVE_FUNCTIONS is not defined. An example is compiling with Intel compilers on Redhat/CentOS 6 targets.
