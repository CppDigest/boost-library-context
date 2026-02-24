# #895 Fix error checking for failure type in example [Merged]

> Username: ndevenish  
> Created at: 2021-08-03 10:15:52 UTC  
> Updated at: 2021-08-13 18:37:08 UTC  
> Merged at: 2021-08-13 18:17:39 UTC  
> Closed at: 2021-08-13 18:17:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/895  

The [example](https://www.boost.org/doc/libs/1_76_0/libs/geometry/doc/html/geometry/reference/algorithms/is_valid/is_valid_2_with_failure_value.html#geometry.reference.algorithms.is_valid.is_valid_2_with_failure_value.example) checking if invalid geometry was fixable with `bg::correct` incorrectly assumes that all failures are fixable, instead of checking for specific failure reasons, as it was purporting to do.  
  
It looks like it was written in bitflag-style, but [`validity_failure_type`](https://www.boost.org/doc/libs/1_76_0/libs/geometry/doc/html/geometry/reference/enumerations/validity_failure_type.html) is not a bitflag field (maybe it used to be?)  
  
Discovered in https://github.com/cctbx/dxtbx/pull/411 as this raised a compiler warning from `-Wint-in-bool-context`:  
  
```  
geom_example.cpp: In function 'int main()':  
geom_example.cpp:34:48: warning: enum constant in boolean context [-Wint-in-bool-context]  
   34 |                            || boost::geometry::failure_wrong_orientation);  
      |                                                ^~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2021-08-04 12:08:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/895#pullrequestreview-722201509  

📁 doc/src/examples/algorithms/is_valid_failure.cpp

```diff
  34 |     bool could_be_fixed = (failure == boost::geometry::failure_not_closed
  35 |-                            || boost::geometry::failure_wrong_orientation);
  35 |+                            || failure == boost::geometry::failure_wrong_orientation);
```

> Username: barendgehrels  
> Created_at: 2021-08-04 12:08:26 UTC  
> Updated_at: 2021-08-04 12:08:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/895#discussion_r682554156  

Thanks!


---
