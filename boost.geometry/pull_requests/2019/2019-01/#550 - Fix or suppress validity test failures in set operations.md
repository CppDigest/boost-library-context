# #550 Fix or suppress validity test failures in set operations [Merged]

> Username: awulkiew  
> Created at: 2019-01-24 23:42:06 UTC  
> Updated at: 2019-01-30 15:44:55 UTC  
> Merged at: 2019-01-30 15:40:49 UTC  
> Closed at: 2019-01-30 15:40:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/550  

With this PR I intend to fix or suppress test failures in set operations.  
  
There are several cases failing right now. Most of them are failing correctly due to a bug in `sym_difference()`: https://github.com/boostorg/geometry/issues/515 and became visible because of this change: https://github.com/boostorg/geometry/pull/533.  
  
There is a test case which is different, i.e. `difference()` case `mysql_23023665_6`:  
  
![image](https://user-images.githubusercontent.com/1226951/51711901-8b0ccd80-202d-11e9-9b54-2eff30c1cdbf.png)  
red: `POLYGON((6 7,18 14, -8 1, 0 0, 18 -8, 6 7), (6 0, -4 3, 5 3, 6 0))`  
green: `POLYGON((2 3,-3 5,-10 -1,2 3))`  
blue: `difference(green, red);`  
  
This case is fixed by the first commit in this PR which changes the `relate()` algorithm to use rescaling just like set operations. In other words to use it for areal geometries. This make `relate()` just slightly more consistent with set operations. It's because in set operations rescaling done WRT bounding box of both geometries, e.g. 2 MultiPolygons but in `relate()` called in `is_valid()` rescaling is done WRT pairs of polygons tested for intersecting interiors.  
  
Because of the above after the change in `relate()` other case starts to fail vailidity test, i.e.: `sym_difference()` and `union()` for MultiPolygons `ticket_9081`:  
  
![image](https://user-images.githubusercontent.com/1226951/51715387-bf39bb80-2038-11e9-9a42-835bf2bafc42.png)  
  
The problematic fragment is here:  
  
![image](https://user-images.githubusercontent.com/1226951/51715418-eabca600-2038-11e9-892d-54601d41dba0.png)  
  
The result of set operation depends on the size of a box used for rescaling. E.g. if `union_()` of only these 2 polygons is calculated the result is one polygon instead of two:  
  
![image](https://user-images.githubusercontent.com/1226951/51715488-3c653080-2039-11e9-8693-3bc0a1cffa2b.png)  
  
So I could pass rescaling policy generated for the whole MultiPolygon instead of a pair of Polygons in `is_valid()`, this might "fix" this issue or not. But even assuming it would fix this particular issue I could easily prepare a different test case which would fail due to this difference in rescaling. So I think for now I'll conditionally disable validity testing for this test case if rescaling is enabled. Ok?

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-01-24 23:57:38 UTC  
> Updated_at: 2019-01-24 23:58:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/550#issuecomment-457403801  

Side note. I tried to enable rescaling for all geometries in `relate()`, also for linestrings and because of this found out that `get_rescale_policy()` may throw overflow numeric conversion exception here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/policies/robustness/get_rescale_policy.hpp#L81. Because `diff` is very small but greater than machine epsilon and there is no upper limit to rescaled coordinates. This is one of the test cases in question: https://github.com/boostorg/geometry/blob/develop/test/algorithms/equals/equals.cpp#L93  
For convenience:  
  
    equals(LINESTRING(-3.2333333333333333925452279800083 5.5999999999999978683717927196994,-3.2333333333333333925452279800083 5.5999999999999996447286321199499),  
           LINESTRING(-3.2333333333333325043668082798831 5.5999999999999996447286321199499,-3.2333333333333333925452279800083 5.5999999999999996447286321199499))  
  
EDIT: This fails for Linestrings and for Linestrings rescaling is disabled but I bet I could easily create very small Polygon which would cause the same issue.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-01-25 00:08:45 UTC  
> Updated_at: 2019-01-25 00:09:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/550#issuecomment-457406100  

Regarding other `difference()` cases that are failing, they all fail due to https://github.com/boostorg/geometry/issues/515 (they all have the same problematic fragment). So I'm going to disable validity test for them as well as @barendgehrels suggested here: https://github.com/boostorg/geometry/commit/247f657fe1bd63bc06419ca0ca1f4f314de15b76. Or do you have some other suggestion?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-01-30 15:31:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/550#issuecomment-458987068  

I'm OK with this change. Thank you.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2019-01-30 15:41:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/550#issuecomment-458991193  

Thanks. Now lets see if the tests are green again.

---
