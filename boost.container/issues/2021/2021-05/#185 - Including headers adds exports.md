# #185 - Including headers adds exports [Closed]

> Username: pdimov  
> Created at: 2021-05-03 01:14:09 UTC  
> Updated at: 2021-05-03 07:19:13 UTC  
> Closed at: 2021-05-03 07:19:13 UTC  
> Url: https://github.com/boostorg/container/issues/185  

When I `#include <boost/json.hpp` on Windows, I get an import library created for my executable, with a bunch of Container exports there. I don't think this should happen. If these symbols are indeed in a library, they need to be marked as imported, not exported; and if they are not supposed to be in a library, they should be marked as visible, not as exported.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-05-03 06:59:57 UTC  
> Url: https://github.com/boostorg/container/issues/185#issuecomment-831064089  

Thanks for the report, you are righ, they should be marked as BOOST_SYMBOL_VISIBLE.
