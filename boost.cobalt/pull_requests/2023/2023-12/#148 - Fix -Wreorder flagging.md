# #148 Fix -Wreorder flagging [Merged]

> Username: 13steinj  
> Created at: 2023-12-27 15:43:18 UTC  
> Updated at: 2024-05-27 00:01:11 UTC  
> Merged at: 2024-05-27 00:01:11 UTC  
> Closed at: 2024-05-27 00:01:11 UTC  
> Url: https://github.com/boostorg/cobalt/pull/148  

If using -Werror=reorder via some cmake build or otherwise, this gets flagged.  
  
Might want to `-Wall -Wextra -Werror` or more in the cmake as well? I'd do it myself but I don't follow the whole "duplicate the lib in the `BOOST_COBALT_IS_ROOT`" case and/or where to move the copyright notice. Also don't know any policies regarding how to set those flags as part of a boost project.

---
