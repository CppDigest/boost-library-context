# #29 refactor: use boost/bind/mem_fn over boost/mem_fn [Merged]

> Username: fanquake  
> Created at: 2022-08-23 15:14:21 UTC  
> Updated at: 2022-08-28 14:30:36 UTC  
> Merged at: 2022-08-24 16:58:43 UTC  
> Closed at: 2022-08-24 16:58:43 UTC  
> Url: https://github.com/boostorg/bind/pull/29  

The later just includes the former. i.e contents of `boost/mem_fn.hpp`:  
```cpp  
#ifndef BOOST_MEM_FN_HPP_INCLUDED  
#define BOOST_MEM_FN_HPP_INCLUDED  
  
// MS compatible compilers support #pragma once  
  
#if defined(_MSC_VER) && (_MSC_VER >= 1020)  
# pragma once  
#endif  
  
//  
//  mem_fn.hpp - a generalization of std::mem_fun[_ref]  
//  
//  Copyright (c) 2009 Peter Dimov  
//  
//  Distributed under the Boost Software License, Version 1.0.  
//  See accompanying file LICENSE_1_0.txt or copy at  
//  http://www.boost.org/LICENSE_1_0.txt  
//  
//  See http://www.boost.org/libs/bind/mem_fn.html for documentation.  
//  
  
#include <boost/bind/mem_fn.hpp>  
  
#endif // #ifndef BOOST_MEM_FN_HPP_INCLUDED  
```

---
