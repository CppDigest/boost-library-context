# #6 Include mpl/if.hpp where needed [Merged]

> Username: glenfe  
> Created at: 2018-09-17 18:15:48 UTC  
> Updated at: 2018-09-17 19:14:49 UTC  
> Merged at: 2018-09-17 19:14:49 UTC  
> Closed at: 2018-09-17 19:14:49 UTC  
> Url: https://github.com/boostorg/property_map/pull/6  

It should not rely on if.hpp being included by some dependency (such as concept_check, which does not depend on MPL anymore).

---
