# #423 utree: Fixed negative value shift [Merged]

> Username: Kojoley  
> Created at: 2018-11-23 12:44:13 UTC  
> Updated at: 2018-11-25 12:36:48 UTC  
> Merged at: 2018-11-25 12:36:44 UTC  
> Closed at: 2018-11-25 12:36:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/423  

Result of shifting a negative value is an implementation defined behavior.  
  
UBSan output:  
```  
boost/spirit/home/support/utree/detail/utree_detail2.hpp:47:48: runtime error: left shift of negative value -128  
    #0 0x45cc72 in boost::spirit::detail::fast_string::tag() const ~/boost-root/./boost/spirit/home/support/utree/detail/utree_detail2.hpp:47:48  
    #1 0x45c94c in boost::spirit::utree::copy(boost::spirit::utree const&) ~/boost-root/./boost/spirit/home/support/utree/detail/utree_detail2.hpp:1502:31  
    #2 0x440cc7 in boost::spirit::utree::operator=(boost::spirit::utree const&) ~/boost-root/./boost/spirit/home/support/utree/detail/utree_detail2.hpp:848:13  
    #3 0x43ce6f in main ~/boost-root/libs/spirit/test/support/utree.cpp:492:16  
    #4 0x7f5d472bc2e0 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x202e0)  
    #5 0x4088c9 in _start (~/boost-root/bin.v2/libs/spirit/test/support/support_utree.test/undefined/clang-linux-7/debug/visibility-hidden/support_utree+0x4088c9)  
```  
  
The temporaries and memcpy calls will be completely optimized out.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-11-23 12:52:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/423#issuecomment-441233977  

There are bunch of other UBs in `utree` implementation, but solving them will require a rewrite of utree and components data layouts what I am not eager to do.

---
