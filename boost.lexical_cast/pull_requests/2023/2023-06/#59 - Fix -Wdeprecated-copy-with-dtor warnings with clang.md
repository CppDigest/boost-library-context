# #59 Fix -Wdeprecated-copy-with-dtor warnings with clang. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2023-06-02 13:19:40 UTC  
> Updated at: 2023-08-12 07:59:06 UTC  
> Merged at: 2023-08-12 07:58:56 UTC  
> Closed at: 2023-08-12 07:58:56 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/59  

This fixes warning like this:  
  
```  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/lexical_cast/bad_lexical_cast.hpp:59:9: error: definition of implicit copy constructor for 'bad_lexical_cast' is deprecated because it has a user-declared destructor  
 [-Werror,-Wdeprecated-copy-with-dtor]  
        ~bad_lexical_cast() BOOST_NOEXCEPT_OR_NOTHROW BOOST_OVERRIDE = default;  
```  
  
Note: this fix requires C++11, so it must wait the merge Boost 1.84 merge window.  
  
The fix proposed in #57 is not enough (explicitly defaulting the destructor doesn't change the warning).

---

## Comment 1

> Username: apolukhin  
> Created_at: 2023-08-12 07:59:06 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/59#issuecomment-1675775918  

Many thanks for the fix!

---
