# #32 - Break dependence on MPL [Closed]

> Username: vinniefalco  
> Created at: 2017-07-30 19:55:53 UTC  
> Updated at: 2017-12-21 18:41:44 UTC  
> Closed at: 2017-12-21 18:41:44 UTC  
> Url: https://github.com/boostorg/utility/issues/32  

Utility depends on MPL just for result_of.hpp. It would be nice if we could break this dependency, as it is circular.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-12-21 18:41:44 UTC  
> Url: https://github.com/boostorg/utility/issues/32#issuecomment-353425914  

Right.  
  
https://github.com/boostorg/utility/commit/7d101d420c01a2a17dc1ebe8d10d0cbea50a7508  
https://github.com/boostorg/utility/commit/9d46de15782ee039aa82b37e6dff6c5cdbe6e67a
