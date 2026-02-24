# #226 Fix float parser missing optimization [Merged]

> Username: MihaiBabiac  
> Created at: 2016-12-02 03:15:48 UTC  
> Updated at: 2016-12-02 07:18:46 UTC  
> Merged at: 2016-12-02 07:18:45 UTC  
> Closed at: 2016-12-02 07:18:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/226  

Fixes [Ticket #12642](https://svn.boost.org/trac/boost/ticket/12642). The optimized pow10 function would not be used unless cfloat had already been externally included.

---
