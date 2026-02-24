# #18 - Change the path to library headers [Closed]

> Username: akrzemi1  
> Created at: 2017-01-18 14:44:42 UTC  
> Updated at: 2018-09-23 22:26:35 UTC  
> Closed at: 2018-08-16 20:03:03 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/18  

As per Boost requirements, all library header files should be located in `include/boost/safe_numerics`.  
  
(Or `include/boost/safe_int`, depending on what the final library name is going to be.)

---

## Comment 1

> Username: akrzemi1  
> Created at: 2017-01-26 09:46:28 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/18#issuecomment-275347098  

In fact, this is not only about meeting Boost requirements, but also for the convenience of the users that want to download the library directly from GitHub. I would really like to include the headers in my program like this:  
  
```c++  
#include <boost/safe_numerics/safe_integer.hpp>  
```  
  
Or at least:  
  
```c++  
#include <safe_numerics/safe_integer.hpp>  
```  
  
Today, neither is possible.

---

## Comment 2

> Username: robertramey  
> Created at: 2018-08-11 17:37:52 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/18#issuecomment-412290509  

OK - I'm at the point where I want to do this.  
  
But I've got a few questions:  
  
a) When I use anything boost, I'm required to do two things:  
  
i) run b2 headers to create a forwarding header in the boost directory  
ii) include via command line or INCLUDE variable to boost root.  
  
b) If I want to use safe_numerics before it is included in the boost distribution, I have to place safe_numerics into it's expected destination in the boost hierarchy and run b2 headers AGAIN to make things match up.  This is even more problematic in that b2 headers is not invoked "automagically" in such a way that people don't know it's there.  
  
To me, this just makes it harder to use / test any new library with boost without dragging in access to a bunch of stuff I may not want.  It inhibits those without beam foo from running the tests etc. This is why I did things the way I did.  
  
Now I'm getting ready to add it to the boost distribution, I'll change this to do it the "boost way".  Not so much because I think it's better but in this case, consistency is better than variety.  But I'm still unsatisfied with the situation.  It's part of the reason that boost is avoided when possible by many people.

---

## Comment 3

> Username: arvidn  
> Created at: 2018-08-13 15:47:36 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/18#issuecomment-412565234  

the way I use boost is to add a dependency to a specific library in my Jamfile, and its include directory is automatically added to my build. I don't need to run `b2 headers`

---

## Comment 4

> Username: robertramey  
> Created at: 2018-08-16 20:03:03 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/18#issuecomment-413667738  

changed to boost custom
