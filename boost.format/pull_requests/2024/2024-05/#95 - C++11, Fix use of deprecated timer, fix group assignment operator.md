# #95 C++11, Fix use of deprecated timer, fix group assignment operator. [Closed]

> Username: jeking3  
> Created at: 2024-05-14 13:49:33 UTC  
> Updated at: 2024-05-14 15:54:43 UTC  
> Closed at: 2024-05-14 15:53:30 UTC  
> Url: https://github.com/boostorg/format/pull/95  

Dependencies like smart_ptr will no longer support C++03 therefore  
the new minimum language level is C++11, and all the CI jobs for  
both C++03 and C++98 have been removed.  
      
Fixed use of the deprecated top level timer class as it was causing  
build problems (warnings-as-errors).  
      
Fixed -Werror=deprecated-cast in group.hpp.  
      
These changes also allow b2 to work properly when executed from  
within the format directory.

---
