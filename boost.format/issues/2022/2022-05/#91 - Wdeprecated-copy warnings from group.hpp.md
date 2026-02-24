# #91 - Wdeprecated-copy warnings from group.hpp. [Closed]

> Username: evoskuil  
> Created at: 2022-05-31 21:32:23 UTC  
> Updated at: 2024-05-15 17:21:06 UTC  
> Closed at: 2024-05-15 17:21:06 UTC  
> Url: https://github.com/boostorg/format/issues/91  

```  
In file included from src/config/printer.cpp:19:  
In file included from ./include/bitcoin/system/config/printer.hpp:25:  
In file included from /home/runner/work/libbitcoin-system/libbitcoin-system/prefixenv/include/boost/format.hpp:48:  
/home/runner/work/libbitcoin-system/libbitcoin-system/prefixenv/include/boost/format/group.hpp:59:12: warning: definition of implicit copy constructor for 'group1<boost::io::detail::group0>' is deprecated because it has a user-declared copy assignment operator [-Wdeprecated-copy]  
   group1& operator=(const group1&);  
           ^  
/home/runner/work/libbitcoin-system/libbitcoin-system/prefixenv/include/boost/format/group.hpp:472:18: note: in implicit copy constructor for 'boost::io::detail::group1<boost::io::detail::group0>' first required here  
group() { return detail::group1< detail::group0 > ( detail::group0() ); }  
                 ^  
  CXX      src/config/libbitcoin_system_la-script.lo  
1 warning generated.  
```  
https://github.com/evoskuil/libbitcoin-system/runs/6664512291?check_suite_focus=true#step:6:2861  
  
Origin: https://www.boost.org/doc/libs/1_79_0/boost/format/group.hpp  
  
This warning repeats heavily in all of versions of boost, and there are no other warnings in our clang builds, which incorporate many boost libraries. There is no apparent workaround apart from global warning suppression.
