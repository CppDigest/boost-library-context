# #416 - Feature request: provide a header of forward-declarations [Closed]

> Username: hadrielk  
> Created at: 2020-09-25 17:59:29 UTC  
> Updated at: 2020-09-25 22:04:17 UTC  
> Closed at: 2020-09-25 22:04:17 UTC  
> Url: https://github.com/boostorg/json/issues/416  

It would be nice if there were a `boost/json_fwd.hpp` that forward-declared the value types.  
  
I haven't looked into the implementation enough to see if that would be impractical, but if it's not then doing so would help with compile times for people who can just forward-declare for their header files.
