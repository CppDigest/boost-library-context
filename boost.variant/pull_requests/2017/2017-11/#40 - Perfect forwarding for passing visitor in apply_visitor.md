# #40 Perfect forwarding for passing visitor in apply_visitor. [Merged]

> Username: paweldac  
> Created at: 2017-11-07 22:58:21 UTC  
> Updated at: 2018-01-23 07:39:52 UTC  
> Merged at: 2018-01-23 07:39:29 UTC  
> Closed at: 2018-01-23 07:39:29 UTC  
> Url: https://github.com/boostorg/variant/pull/40  

Added apply_visitor with Visitor&&. Allows to bind Visitor&&, Visitor& and const Visitor&.  
Removed apply_visitor with const Visitor& since it was catching inlined Visitors&&.  
Passing const Visitor to result_wrapper resulted in compilation error when Visitor was mutable.  
  
Fixes: https://github.com/boostorg/variant/issues/39

---

## Comment 1

> Username: paweldac  
> Created_at: 2017-11-07 22:59:27 UTC  
> Updated_at: 2017-11-07 23:00:26 UTC  
> Url: https://github.com/boostorg/variant/pull/40#issuecomment-342652475  

tests are passing: https://travis-ci.org/paweldac/variant/builds/298816119

---

## Comment 2

> Username: paweldac  
> Created_at: 2017-11-17 15:52:42 UTC  
> Url: https://github.com/boostorg/variant/pull/40#issuecomment-345281459  

@apolukhin what do you think?

---

## Comment 3

> Username: apolukhin  
> Created_at: 2018-01-23 07:39:52 UTC  
> Url: https://github.com/boostorg/variant/pull/40#issuecomment-359702493  

Many thanks!

---
