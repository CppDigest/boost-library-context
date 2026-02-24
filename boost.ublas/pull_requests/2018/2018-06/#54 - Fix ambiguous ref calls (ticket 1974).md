# #54 Fix ambiguous ref calls (ticket 1974) [Open]

> Username: bwhaines  
> Created at: 2018-06-15 20:22:53 UTC  
> Updated at: 2019-03-03 13:59:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/54  

This fixes the issue outlined in [ticket 1974](https://svn.boost.org/trac10/ticket/1974) by applying Will Moss' patch, so calls to `ref` will no longer cause ambiguous overloaded function errors during compilation.

---
