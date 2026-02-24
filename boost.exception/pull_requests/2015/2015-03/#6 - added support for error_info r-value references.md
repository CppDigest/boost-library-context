# #6 added support for error_info r-value references [Closed]

> Username: rnburn  
> Created at: 2015-03-21 05:48:59 UTC  
> Updated at: 2018-11-15 05:38:21 UTC  
> Closed at: 2018-11-15 05:38:21 UTC  
> Url: https://github.com/boostorg/exception/pull/6  

adds support to transport exception data using move semantics  
  
Example:  
  
```  
boost::exception e;  
typedef boost::error_info<struct tag,Data> ErrorInfo;  
Data data;  
e << ErrorInfo(std::move(data));  
throw e;  
```

---

## Comment 1

> Username: apolukhin  
> Created_at: 2015-03-21 07:26:20 UTC  
> Updated_at: 2015-03-22 05:29:39 UTC  
> Url: https://github.com/boostorg/exception/pull/6#discussion_r26889889  

use `shared_ptr<error_info_tag_t> p = make_shared<error_info_tag_t>(std::move(v))`  
It calls less memory allocations

---

## Comment 2

> Username: apolukhin  
> Created_at: 2015-03-21 07:29:26 UTC  
> Updated_at: 2015-03-22 05:29:39 UTC  
> Url: https://github.com/boostorg/exception/pull/6#discussion_r26889905  

Check for `BOOST_NO_CXX11_DEFAULTED_FUNCTIONS` and define function bodies by hand in worst case.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2015-03-21 07:36:26 UTC  
> Updated_at: 2015-03-22 05:29:39 UTC  
> Url: https://github.com/boostorg/exception/pull/6#discussion_r26889931  

Add `#include <utility>` for `std::move`

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2015-03-21 10:59:41 UTC  
> Updated_at: 2015-03-22 05:29:39 UTC  
> Url: https://github.com/boostorg/exception/pull/6#discussion_r26891047  

Also, Visual C++ 12 doesn't support defaulted move constructors and move assignment operators even though it supports defaulted functions in general.

---

## Comment 5

> Username: rnburn  
> Created_at: 2015-03-22 05:34:11 UTC  
> Url: https://github.com/boostorg/exception/pull/6#issuecomment-84524764  

I removed the default constructor and assignment operators (just implemented all of them to avoid the problem with Visual C++ 12); changed to use make_shared; and added the missing header files.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2016-12-21 19:53:17 UTC  
> Url: https://github.com/boostorg/exception/pull/6#issuecomment-268622561  

Looks like this was fixed in 5a040eedf54691d202e3099033d6d1c5c88a736e

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-11-14 02:38:57 UTC  
> Url: https://github.com/boostorg/exception/pull/6#issuecomment-438516511  

@zajo perhaps is this was already fixed, the PR could be closed?

---
