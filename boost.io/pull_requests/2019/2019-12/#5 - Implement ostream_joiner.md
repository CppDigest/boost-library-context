# #5 Implement ostream_joiner [Merged]

> Username: glenfe  
> Created at: 2019-12-14 13:42:18 UTC  
> Updated at: 2019-12-14 16:24:27 UTC  
> Merged at: 2019-12-14 16:24:26 UTC  
> Closed at: 2019-12-14 16:24:26 UTC  
> Url: https://github.com/boostorg/io/pull/5  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2019-12-14 15:43:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/io/pull/5#pullrequestreview-332219474  

📁 doc/ostream_joiner.adoc

```diff
  34 |+ {
  35 |+     std::vector<int> v{ 2, 4, 6, 8 };
  36 |+     std::copy(begin(v), end(v), boost::make_ostream_joiner(std::cout, ", "));
```

> Username: pdimov  
> Created_at: 2019-12-14 15:43:38 UTC  
> Updated_at: 2019-12-14 15:56:12 UTC  
> Url: https://github.com/boostorg/io/pull/5#discussion_r357924223  

C++11 example due to begin(v).


---
