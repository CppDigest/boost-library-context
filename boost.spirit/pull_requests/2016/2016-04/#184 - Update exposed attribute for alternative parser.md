# #184 Update exposed attribute for alternative parser [Merged]

> Username: vtnerd  
> Created at: 2016-04-08 01:29:37 UTC  
> Updated at: 2016-04-09 10:17:05 UTC  
> Merged at: 2016-04-09 10:17:05 UTC  
> Closed at: 2016-04-09 10:17:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/184  

Removed `parse_into_container_impl` specializations for `alternative` and `expect`. `alternative` now exposes the inner attribute directly when both branches are the same. All tests pass, including test case from [bug 12094](https://svn.boost.org/trac/boost/ticket/12094).  
  
If the alternative parser has matching two element sequences, the sequence is not correctly flattened with the neighboring sequence, i.e.:  
  
``` c++  
int_ >> ((int_ >> int_) | (int_ >> int_))  
```  
  
 This will be done next.

---
