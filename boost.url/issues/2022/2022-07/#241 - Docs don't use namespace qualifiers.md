# #241 - Docs don't use namespace qualifiers [Closed]

> Username: vinniefalco  
> Created at: 2022-07-23 22:09:31 UTC  
> Updated at: 2022-08-03 18:12:27 UTC  
> Closed at: 2022-08-03 18:12:27 UTC  
> Url: https://github.com/boostorg/url/issues/241  

Example code in the documentation and javadocs should be written as if the following declarations are in effect:  
```  
#include <boost/url.hpp>  
using namespace boost::urls;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-23 22:10:04 UTC  
> Url: https://github.com/boostorg/url/issues/241#issuecomment-1193195404  

This has to wait until my refactoring is done or else there will be additional unnecessary merge conflicts.
