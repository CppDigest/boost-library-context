# #11 Fix error flagged by -fsanitize=nonnull-attribute [Open]

> Username: davido  
> Created at: 2017-01-09 14:00:31 UTC  
> Updated at: 2017-01-09 14:00:31 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/11  

Summary:  
  
this fixes the error reported by -fsanitize=nonnull-attribute analyzer:  
  
/workdir/UnpackedTarball/boost/boost/circular_buffer/debug.hpp:37:17:  
runtime error: null pointer passed as argument 1, which is declared to never be null  
> /usr/include/string.h:62:62: note: nonnull attribute specified here

---
