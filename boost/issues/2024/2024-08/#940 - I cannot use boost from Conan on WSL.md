# #940 - [question] I cannot use boost from Conan on WSL [Open]

> Username: markspacehub  
> Created at: 2024-08-19 08:46:06 UTC  
> Updated at: 2024-08-19 14:21:11 UTC  
> Url: https://github.com/boostorg/boost/issues/940  

I am trying to use Boost's dynamic_bitset class, my project currently uses Conan to get its dependencies and CMake to build. I added Boost to Conan's requires and then I used CMake to `find_package(Boost REQUIRED)` and `target_link_libraries(MyProject PRIVATE boost::boost)`.   
When I call conan install . it installs boost and then when I configure with CMake, it finds boost with all the header files etc.  
  
When I build the project however, there is a chain of linked header files that ends with `#include <boost/compute/cl/hpp>`. This in turn tries to `#include <CL/cl.h>` which does not exist on my system, I can try and force define __APPLE__ to try and `#include <OpenCL/cl.h>` instead, but again it doesnt appear to exist for me.  
  
I am using WSL (v2.2.4) Ubuntu (v 24.04) to both build and run the application.   
  
Why can I not find cl.h, why do I need it and what can I do to fix this?  
  
Thanks :)

---

## Comment 1

> Username: mclow  
> Created at: 2024-08-19 14:21:10 UTC  
> Url: https://github.com/boostorg/boost/issues/940#issuecomment-2296701564  

You will probably get more responses if you post this to the boost-users mailing list.  
You can subscribe here: https://lists.boost.org/mailman/listinfo.cgi/boost-users
