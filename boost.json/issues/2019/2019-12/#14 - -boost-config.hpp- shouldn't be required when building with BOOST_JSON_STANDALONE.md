# #14 - <boost/config.hpp> shouldn't be required when building with BOOST_JSON_STANDALONE [Closed]

> Username: msimonsson  
> Created at: 2019-12-02 09:33:38 UTC  
> Updated at: 2019-12-04 03:21:17 UTC  
> Closed at: 2019-12-04 03:21:17 UTC  
> Url: https://github.com/boostorg/json/issues/14  

For example:   
  
https://github.com/vinniefalco/json/blob/develop/include/boost/json/detail/config.hpp#L13  
  
```  
#include <boost/config.hpp>  
#ifndef BOOST_JSON_STANDALONE  
# include <boost/assert.hpp>  
# include <boost/system/error_code.hpp>  
# include <boost/system/system_error.hpp>  
# include <boost/utility/string_view.hpp>  
#else  
# include <cassert>  
# include <string_view>  
# include <system_error>  
#endif  
#include <stdint.h>  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-02 11:03:38 UTC  
> Url: https://github.com/boostorg/json/issues/14#issuecomment-560347450  

DOH!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-12-03 04:01:16 UTC  
> Url: https://github.com/boostorg/json/issues/14#issuecomment-560988220  

This should be fixed in the _develop_ branch
