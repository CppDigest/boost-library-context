# #164 - Disabling runtime type identification feature cause an error during compilation [Closed]

> Username: cngzhnp  
> Created at: 2021-12-31 13:28:20 UTC  
> Updated at: 2024-07-06 21:09:32 UTC  
> Closed at: 2024-07-06 21:09:32 UTC  
> Url: https://github.com/boostorg/interprocess/issues/164  

Boost Version : 1.78  
GCC Version : 11.2.0  
  
When we try to compile `Boost::Interprocess` component with disabling **RTTI** (via `-fno-rtti` option with GCC) cause a compilation error due to standard `typeid `implementation.  
  
Here is the simple build command and result with `-fno-rtti ` option:  
  
`g++ -Wall -fno-rtti detail/in_place_interface.hpp -I ../../ -o wout_rtti`  
  
```  
detail/in_place_interface.hpp:19:11: warning: #pragma once in main file  
   19 | #  pragma once  
      |           ^~~~  
detail/in_place_interface.hpp: In constructor ‘boost::interprocess::ipcdetail::placement_destroy<T>::placement_destroy()’:  
detail/in_place_interface.hpp:56:15: error: cannot use ‘typeid’ with ‘-fno-rtti’  
   56 |       typeid(T).name())  
      |               ^  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-07-06 21:09:32 UTC  
> Url: https://github.com/boostorg/interprocess/issues/164#issuecomment-2211969062  

Reviewing old bugs.  
  
Unfortunately Boost.Interprocess is not designed to build without RTTI or exceptions and there are no plans to support those systems as environments with shared memory or memory mapped files are supposedly complex enough to afford them.
