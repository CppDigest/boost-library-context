# #204 - Coverity Scan noted possible issue in period() code [Closed]

> Username: jeking3  
> Created at: 2022-03-01 14:13:16 UTC  
> Updated at: 2025-07-07 01:06:41 UTC  
> Closed at: 2025-07-07 01:06:41 UTC  
> Url: https://github.com/boostorg/date_time/issues/204  

I have not looked into it, wanted to make sure it got looked at.  It's talking about line 304 specifically.  
  
https://scan4.scan.coverity.com/reports.htm#v17121/p13946/fileInstanceId=114637940&defectInstanceId=14388704&mergedDefectId=255065  
  
```  
template<class point_rep, class duration_rep>  
299  inline BOOST_CXX14_CONSTEXPR  
300  bool period<point_rep,duration_rep>::intersects(const period<point_rep,duration_rep>& other) const  
301  {   
   	original: other.begin_ < this->begin_ looks like the original copy.  
   	  
CID 255065 (#1 of 1): Copy-paste error (COPY_PASTE_ERROR)  
copy_paste_error: begin_ in other.last_ >= this->begin_ looks like a copy-paste error.  
   	Should it say last_ instead?  
302    return ( contains(other.begin_) ||  
303             other.contains(begin_) ||  
304             ((other.begin_ < begin_) && (other.last_ >= begin_)));  
305  }  
306  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2025-07-06 18:43:51 UTC  
> Updated at: 2025-07-06 18:55:34 UTC  
> Url: https://github.com/boostorg/date_time/issues/204#issuecomment-3042333901  

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
