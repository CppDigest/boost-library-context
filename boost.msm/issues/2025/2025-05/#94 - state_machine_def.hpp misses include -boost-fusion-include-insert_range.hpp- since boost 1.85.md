# #94 - state_machine_def.hpp misses include <boost/fusion/include/insert_range.hpp> since boost 1.85 [Closed]

> Username: tougantium  
> Created at: 2025-05-27 10:20:56 UTC  
> Updated at: 2025-05-27 12:53:43 UTC  
> Closed at: 2025-05-27 12:53:42 UTC  
> Url: https://github.com/boostorg/msm/issues/94  

Hi,  
  
when trying to compile the following code (starting with boost 1.85, see [compiler explorer](https://godbolt.org/z/e43xccv9T))  
  
```  
#include <boost/msm/front/state_machine_def.hpp>  
  
int main()  
{}  
```  
  
I get the following compile error  
  
```  
In file included from /app/boost/include/boost/msm/front/state_machine_def.hpp:21,  
                 from <source>:2:  
/app/boost/include/boost/msm/front/states.hpp:57:46: error: 'insert_range' in namespace 'boost::fusion::result_of' does not name a template type [-Wtemplate-body]  
   57 |         typename ::boost::fusion::result_of::insert_range<  
      |                                              ^~~~~~~~~~~~  
/app/boost/include/boost/msm/front/states.hpp:57:58: error: expected template-argument before '<' token [-Wtemplate-body]  
   57 |         typename ::boost::fusion::result_of::insert_range<  
      |                                                          ^  
/app/boost/include/boost/msm/front/states.hpp:57:58: error: expected '>' before '<' token [-Wtemplate-body]  
/app/boost/include/boost/msm/front/states.hpp:62:5: error: template argument 1 is invalid [-Wtemplate-body]  
   62 |     >::type type;  
      |     ^  
/app/boost/include/boost/msm/front/states.hpp:62:6: error: '<declaration error>' is not a template [-Wtemplate-body]  
   62 |     >::type type;  
      |      ^~  
ASM generation compiler returned: 1  
In file included from /app/boost/include/boost/msm/front/state_machine_def.hpp:21,  
                 from <source>:2:  
/app/boost/include/boost/msm/front/states.hpp:57:46: error: 'insert_range' in namespace 'boost::fusion::result_of' does not name a template type [-Wtemplate-body]  
   57 |         typename ::boost::fusion::result_of::insert_range<  
      |                                              ^~~~~~~~~~~~  
/app/boost/include/boost/msm/front/states.hpp:57:58: error: expected template-argument before '<' token [-Wtemplate-body]  
   57 |         typename ::boost::fusion::result_of::insert_range<  
      |                                                          ^  
/app/boost/include/boost/msm/front/states.hpp:57:58: error: expected '>' before '<' token [-Wtemplate-body]  
/app/boost/include/boost/msm/front/states.hpp:62:5: error: template argument 1 is invalid [-Wtemplate-body]  
   62 |     >::type type;  
      |     ^  
/app/boost/include/boost/msm/front/states.hpp:62:6: error: '<declaration error>' is not a template [-Wtemplate-body]  
   62 |     >::type type;  
      |      ^~  
Execution build compiler returned: 1  
Build failed  
```  
  
When I manually add  
  
```  
#include <boost/fusion/include/insert_range.hpp>  
```  
  
the compilation will succeed. I guess the include is missing in `states.hpp`.  
  
Many thanks in advance for your help and for your great work!

---

## Comment 1

> Username: tougantium  
> Created at: 2025-05-27 12:53:42 UTC  
> Url: https://github.com/boostorg/msm/issues/94#issuecomment-2912409441  

fixed in 1.87
