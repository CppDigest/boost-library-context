# #132 - fatal error: 'stdexception' file not found [Closed]

> Username: mtak-  
> Created at: 2017-06-25 19:24:38 UTC  
> Updated at: 2017-06-25 20:38:33 UTC  
> Closed at: 2017-06-25 20:38:33 UTC  
> Url: https://github.com/boostorg/fiber/issues/132  

Building from head.  
  
`numa/topology.cpp` and `numa/pin_thread.cpp` both `#include <stdexception>`.  
  
I think this is intended to be `#include <stdexcept>`.

---

## Comment 1

> Username: olk  
> Created at: 2017-06-25 20:38:33 UTC  
> Url: https://github.com/boostorg/fiber/issues/132#issuecomment-310926760  

fixed, ty
