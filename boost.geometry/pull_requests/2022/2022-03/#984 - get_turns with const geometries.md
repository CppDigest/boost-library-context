# #984 [fix] get_turns with const geometries [Merged]

> Username: barendgehrels  
> Created at: 2022-03-02 14:02:45 UTC  
> Updated at: 2022-04-07 12:09:48 UTC  
> Merged at: 2022-03-09 11:15:22 UTC  
> Closed at: 2022-03-09 11:15:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/984  

Fixes #983   
  
After this, the using operations such as `disjoint` do still not work yet - they need to use the `helper_geometry_type` internally to calculate turns, etc.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-03-02 14:03:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/984#pullrequestreview-897616239  

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 164 |             advance_to_non_duplicate_next(m_current_point, m_circular_iterator);
 164 |-             m_point = *m_circular_iterator;
 165 |+             m_point = boost::addressof(*m_circular_iterator);
```

> Username: barendgehrels  
> Created_at: 2022-03-02 14:03:32 UTC  
> Updated_at: 2022-03-02 14:05:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/984#discussion_r817720087  

Fix 3 (required for const points without a constructor)

> Username: awulkiew  
> Created_at: 2022-03-02 15:00:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/984#discussion_r817776335  

Actually it's copy assignment. Are you sure we don't require it? Is it tested anywhere? Where does it fail?

> Username: barendgehrels  
> Created_at: 2022-03-03 12:34:13 UTC  
> Updated_at: 2022-03-03 12:34:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/984#discussion_r818611852  

Right, copy assignment. So it does not work with certain point types (such as the one I provided).  
  
Now I see your question, I'm considering why we can't pass the result of the iterator anyway, without a point. I'll check it later.

> Username: barendgehrels  
> Created_at: 2022-03-09 09:48:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/984#discussion_r822467155  

Fixed, I return the iterator now.  
That point is retrieved lazily, because often it's not necessary. Only when asked for the circular iterator moves forward to the point, that happens only once. Then it can just return that point (as also happens later in that same header file).

> Username: awulkiew  
> Created_at: 2022-03-09 10:51:23 UTC  
> Updated_at: 2022-03-09 10:53:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/984#discussion_r822522663  

Right, this is better.  
  
I'm wondering which point types didn't work with the previous version? It seems wierd to me that we allow points that cannot be copied or assigned to. Especially in functions that work for containers/ranges and in order to store a point in a container one has to copy or move assign. So I have a problem with figuring out the case we talk about here.

> Username: barendgehrels  
> Created_at: 2022-03-09 11:17:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/984#discussion_r822542919  

I'm talking about point types like here:  
https://github.com/boostorg/geometry/blob/develop/test/test_geometries/const_point.hpp#L16  
  
It happens in practice...

> Username: awulkiew  
> Created_at: 2022-03-09 15:24:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/984#discussion_r822767581  

This class has implicitly-declared copy and move assignment operators and constructors. There is nothing preventing assignment here. There are only no setters for coordinates. E.g. this compiles:  
```  
const_point pt(0, 0);  
const_point pt2 = pt;  
const_point pt3 = std::move(pt);  
pt2 = pt;  
pt3 = std::move(pt);  
```


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2022-03-02 14:04:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/984#pullrequestreview-897617871  

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 172 |-         typedef typename robust_point_type<Point, RobustPolicy>::type robust_point_type;
 173 |+         using box_point_type = typename geometry::point_type<typename Section::box_type>::type;
 174 |+         using robust_point_type = typename robust_point_type<box_point_type, RobustPolicy>::type;
```

> Username: barendgehrels  
> Created_at: 2022-03-02 14:04:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/984#discussion_r817721141  

Fix 1


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2022-03-02 14:04:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/984#pullrequestreview-897618062  

📁 include/boost/geometry/algorithms/detail/sections/section_functions.hpp

```diff
 133 |-     typename geometry::robust_point_type<Point, RobustPolicy>::type robust_point;
 133 |+     using box_point_type = typename geometry::point_type<Box>::type;
 134 |+     typename geometry::robust_point_type<box_point_type, RobustPolicy>::type robust_point;
```

> Username: barendgehrels  
> Created_at: 2022-03-02 14:04:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/984#discussion_r817721265  

Fix 2


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2022-03-09 11:01:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/984#pullrequestreview-904196340  

Looks OK to me. Thanks!

---
