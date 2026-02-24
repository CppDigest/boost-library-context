# #301 bug-fix: handling of special values in ctor and in value method [Merged]

> Username: HDembinski  
> Created at: 2020-11-10 21:08:10 UTC  
> Updated at: 2020-11-10 21:20:06 UTC  
> Merged at: 2020-11-10 21:18:33 UTC  
> Closed at: 2020-11-10 21:18:33 UTC  
> Url: https://github.com/boostorg/histogram/pull/301  

Fixed  
* Passing nan to the ctor of axis::variable failed to throw std::invalid_argument  
* axis::variable{{0, 1, inf}}.value(1) was nan, because interpolation would compute 0 * inf

---

## Comment 1

> Username: HDembinski  
> Created_at: 2020-11-10 21:09:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/301#issuecomment-724967889  

Affected by bug and fix: boost-histogram, related issue https://github.com/scikit-hep/boost-histogram/issues/468

---
