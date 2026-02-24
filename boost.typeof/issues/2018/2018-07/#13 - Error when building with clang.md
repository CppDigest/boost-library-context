# #13 - Error when building with clang [Open]

> Username: krojew  
> Created at: 2018-07-26 09:02:30 UTC  
> Updated at: 2020-08-13 19:15:21 UTC  
> Url: https://github.com/boostorg/typeof/issues/13  

When building with clang with all warnings and -Werror:  
  
error: extension used [-Werror,-Wlanguage-extension-token]  
#include BOOST_TYPEOF_INCREMENT_REGISTRATION_GROUP()  
         ^  
boost/typeof/typeof.hpp:197:13: note: expanded from macro 'BOOST_TYPEOF_INCREMENT_REGISTRATION_GROUP'  
     <boost/typeof/incr_registration_group.hpp>

---

## Comment 1

> Username: golgobot  
> Created at: 2020-08-13 19:15:21 UTC  
> Url: https://github.com/boostorg/typeof/issues/13#issuecomment-673661487  

I'm getting the same error when I use `bcp` to import boost libs. Is there any update on this?
