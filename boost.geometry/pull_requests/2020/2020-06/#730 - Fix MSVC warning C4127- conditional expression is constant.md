# #730 Fix MSVC warning C4127: conditional expression is constant [Merged]

> Username: mloskot  
> Created at: 2020-06-19 15:38:43 UTC  
> Updated at: 2020-06-19 17:13:03 UTC  
> Merged at: 2020-06-19 17:12:59 UTC  
> Closed at: 2020-06-19 17:12:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/730  

Fixes #727   
  
-----  
  
MWE that triggers the warning  
  
```cpp  
#include <deque>  
#include <boost/geometry.hpp>  
namespace bg = boost::geometry;  
int main()  
{  
    using line_t = bg::model::linestring<bg::model::d2::point_xy<double>>;  
    line_t green, blue;  
  
    bg::read_wkt("LINESTRING(0 0, 2 0)", green);  
    bg::read_wkt("LINESTRING(-1 -1, -1 2)", blue);  
  
    std::deque<line_t> output;  
    bg::union_(green, blue, output);  
    bg::intersection(green, blue, output);  
}  
```  
  
------  
  
It's worth to be aware usage of the macro  
  
https://github.com/boostorg/geometry/blob/cff2ee9d6c30afba4fe6cf64b3d65080372f8a5d/include/boost/geometry/util/condition.hpp#L35  
  
may trigger the MSVC [code analysis warning C6319](https://docs.microsoft.com/en-us/cpp/code-quality/c6319).

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-06-19 15:52:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/730#pullrequestreview-434181698  

📁 include/boost/geometry/algorithms/detail/overlay/follow.hpp

```diff
 490 |-             else if (FollowIsolatedPoints
 491 |-                   && following::is_touching(*it, *iit, entered))
 490 |+             else if (BOOST_GEOMETRY_CONDITION(FollowIsolatedPoints && following::is_touching(*it, *iit, entered)))
```

> Username: awulkiew  
> Created_at: 2020-06-19 15:52:18 UTC  
> Updated_at: 2020-06-19 16:09:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/730#discussion_r442918014  

Why have you decided to put it in one line instead of keeping the old layout? I.e.:  
```  
else if (BOOST_GEOMETRY_CONDITION(FollowIsolatedPoints)  
      && following::is_touching(*it, *iit, entered))  
```

> Username: mloskot  
> Created_at: 2020-06-19 16:01:36 UTC  
> Updated_at: 2020-06-19 16:09:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/730#discussion_r442922932  

None. Perhaps just a legacy and paranoid habit to fit a macro invocation in a single line.  
  
I've reverted the formatting.


---

## Review 2 [Approved]

> Username: awulkiew  
> Created_at: 2020-06-19 16:06:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/730#pullrequestreview-434192147  

Thanks!  
Btw, is the `util/condition.hpp` header included by the dependencies of these files? If it is not, then it should probably be added.

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-06-19 16:10:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/730#issuecomment-646720525  

Good catch, thanks! Fixed.

---
