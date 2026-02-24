# #242 issue-204: period::intersects incorrect for empty periods [Merged]

> Username: jeking3  
> Created at: 2025-07-06 19:00:01 UTC  
> Updated at: 2025-07-07 01:31:09 UTC  
> Merged at: 2025-07-07 01:06:39 UTC  
> Closed at: 2025-07-07 01:06:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/242  

Originally identified by Coverity Scan.  Zero length periods like [3, 3) cannot intersect with anything.  Invalid periods like [5, 4) also cannot intersect with anything.  In both cases the unit tests are wrong, and fixing them reveals the implementation is not canonical for half-open sane intervals.  
  
In testgeneric_period.cpp there is a bug on lines 128-129; again on 149-150:  
  
```  
  check("Intersects", zero_len.intersects(p1));  
  check("Intersects", p1.intersects(zero_len));  
...  
  check("Intersects", null_per.intersects(p1));  
  check("Intersects", p1.intersects(null_per));  
```  
  
Empty (null) periods cannot intersect with anything, so the test is wrong.  Fixing the test reveals the implementation is wrong.  
  
On simplifying the implementation of intersects to use the half-open canonical check (ensuring the half-open intervals are sane), it now passes.  
  
```  
  template<class point_rep, class duration_rep>  
  inline BOOST_CXX14_CONSTEXPR  
  bool period<point_rep,duration_rep>::intersects(const period<point_rep,duration_rep>& other) const  
  {   
    return !is_null() && !other.is_null() && (begin_ < other.end()) && (other.begin_ < end());  
  }  
...  
  check("Intersects", !zero_len.intersects(p1));  
  check("Intersects", !p1.intersects(zero_len));  
...  
  check("Intersects", !null_per.intersects(p1));  
  check("Intersects", !p1.intersects(null_per));  
  ***passed***  
```  
  
This fixes #204

---

## Review 1 [Approved]

> Username: JeffGarland  
> Created_at: 2025-07-07 01:05:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/date_time/pull/242#pullrequestreview-2991892006  

This looks correct to me

---

## Comment 2

> Username: JeffGarland  
> Created_at: 2025-07-07 01:06:24 UTC  
> Url: https://github.com/boostorg/date_time/pull/242#issuecomment-3043212994  

The CI issues seem spurious.

---
