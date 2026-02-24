# #73 - boost/1.85.0: include boost/msm/front/state.hpp will cause build error [Closed]

> Username: auzn88  
> Created at: 2024-07-16 12:47:12 UTC  
> Updated at: 2024-07-17 15:05:36 UTC  
> Closed at: 2024-07-17 15:05:36 UTC  
> Url: https://github.com/boostorg/msm/issues/73  

Though unnecessary, but if we include `boost/msm/front/states.hpp` before include `boost/msm/front/state_machine_def.hpp`, it will cause compilation error.  Using the following tutorial program as example:  
  
[CompositeTutorial.cpp](https://github.com/boostorg/msm/blob/develop/doc/HTML/examples/CompositeTutorial.cpp)  
  
If we add `#include <boost/msm/front/states.hpp>` right after `#include <iostream>`, like this:  
```c++  
#include <iostream>  
  
#include <boost/msm/front/states.hpp>  // <-- add this header here   
// back-end  
#include <boost/msm/back11/state_machine.hpp>  
//front-end  
#include <boost/msm/front/state_machine_def.hpp>  
...  
```  
  
 then compiling it will give the following compilation error:  
  
```  
In file included from CompositeTutorial.cpp:12:  
./boost/msm/front/states.hpp:57:46: error: ‘insert_range’ in namespace ‘boost::fusion::result_of’ does not name a template type  
   57 |         typename ::boost::fusion::result_of::insert_range<  
      |                                              ^~~~~~~~~~~~  
./boost/msm/front/states.hpp:57:58: error: expected template-argument before ‘<’ token  
   57 |         typename ::boost::fusion::result_of::insert_range<  
      |                                                          ^  
./boost/msm/front/states.hpp:57:58: error: expected ‘>’ before ‘<’ token  
./boost/msm/front/states.hpp:62:5: error: template argument 1 is invalid  
   62 |     >::type type;  
      |     ^  
./boost/msm/front/states.hpp:62:6: error: ‘<declaration error>’ is not a template [-fpermissive]  
   62 |     >::type type;  
      |      ^~  
```  
  
tested using gcc9 & gcc13. Only 1.85 has this issue
