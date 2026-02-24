# #29 - icl/iterator.hpp and icl/detail/element_iterator.hpp include <boost/config/warning_disable.hpp> [Open]

> Username: pgroke-dt  
> Created at: 2020-10-12 11:46:57 UTC  
> Updated at: 2021-12-21 15:44:34 UTC  
> Url: https://github.com/boostorg/icl/issues/29  

`icl\iterator.hpp` and `icl\detail\element_iterator.hpp` include `<boost/config/warning_disable.hpp>`. This disabled the MSVC warning C4996 without re-enabling it again.  
  
For context: C4996 is MSVC's warning for using deprecated stuff. This means code that uses Boost.Icl will not get warning C4996 which IMO is quite bad. `<boost/config/warning_disable.hpp>` even contains the following comment:  
```  
//  IT SHOULD NOT BE INCLUDED BY ANY BOOST HEADER.  
//  
//  YOU SHOULD NOT INCLUDE IT IF YOU CAN REASONABLY FIX THE WARNING.  
```

---

## Comment 1

> Username: brunom  
> Created at: 2021-12-21 15:44:33 UTC  
> Url: https://github.com/boostorg/icl/issues/29#issuecomment-998886158  

Still broken in 1.78
