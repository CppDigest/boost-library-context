# #96 Make annotation.hpp generic and move to support/utility/annotate_on_success.hpp. [Merged]

> Username: mlang  
> Created at: 2015-05-18 12:37:53 UTC  
> Updated at: 2015-05-18 20:28:05 UTC  
> Merged at: 2015-05-18 20:27:48 UTC  
> Closed at: 2015-05-18 20:27:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/96  

Since X3 promotes x3::variant, we can actually make annotation.hpp generic,  
avoiding copy-pastism in all the projects that make use of basic error  
reporting.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-05-18 20:27:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/96#issuecomment-103199772  

Wonderful! I wanted to do that soon. You beat me to it. Thanks, Mario! You rock!

---

## Comment 2

> Username: djowel  
> Created_at: 2015-05-18 20:28:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/96#issuecomment-103199839  

Next step would be error handler :-)

---
