# #61 - Visual C++ /Wall: there is no warning number [....] [Closed]

> Username: mehrdadn  
> Created at: 2018-06-03 20:46:23 UTC  
> Updated at: 2018-07-24 17:45:01 UTC  
> Closed at: 2018-07-24 17:45:01 UTC  
> Url: https://github.com/boostorg/regex/issues/61  

If you try to compile this with `/Wall` under Visual C++  
  
	#include <boost/regex.hpp>  
	int main() { return 0; }  
  
then depending on the version, it emits warnings that different warnings are unavailable:  
  
- On `_MSC_VER == 1500`, I get    
	`boost\regex\config.hpp(206): warning C4619: #pragma warning: there is no warning number '4660'`  
- (I'm not sure what happens on `_MSC_VER == 1600`.)  
- On `_MSC_VER == 1700`, I get    
	`boost\regex\config.hpp(204) : warning C4619: #pragma warning : there is no warning number '4231'`  
  
Note that this isn't just a problem in `boost\regex\config.hpp`—the same problem occurs in `boost\regex\v4\basic_regex.hpp`, `boost\regex\v4\match_results.hpp`, `boost\regex\v4\instances.hpp`, etc.  
  
It would be great if this were addressed, as this unfortunately pollutes warnings in user code.  
  
Thank you!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-07-24 17:45:01 UTC  
> Url: https://github.com/boostorg/regex/issues/61#issuecomment-407492591  

This should be fixed now in develop.
