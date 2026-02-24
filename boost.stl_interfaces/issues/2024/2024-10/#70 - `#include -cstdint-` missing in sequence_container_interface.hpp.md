# #70 - `#include <cstdint>` missing in sequence_container_interface.hpp [Closed]

> Username: thebrandre  
> Created at: 2024-10-11 05:51:57 UTC  
> Updated at: 2024-10-12 06:43:10 UTC  
> Closed at: 2024-10-12 06:43:09 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/70  

`#include <cstdint>` is needed before `<boost/stl_interfaces/sequence_container_interface.hpp>` for the definition of [SIZE_MAX](https://en.cppreference.com/w/cpp/types/climits).  
  
Easy to reproduce on Compiler Explorer with the sample I added to the other bug report [here](https://godbolt.org/z/fnxxaxo9x).  
Simply remove the line  `#include <cstdint>` .

---

## Comment 1

> Username: thebrandre  
> Created at: 2024-10-12 06:43:09 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/70#issuecomment-2408421711  

I just noticed that this issue is a duplicate of #63 and it is already fixed. Sorry about that!
