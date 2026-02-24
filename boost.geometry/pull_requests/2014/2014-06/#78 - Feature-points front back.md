# #78 Feature/points front back [Closed]

> Username: mkaravel  
> Created at: 2014-06-27 01:37:37 UTC  
> Updated at: 2014-06-30 08:18:59 UTC  
> Closed at: 2014-06-30 08:18:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/78  

Fix return type of points_front and points_back: they should return a const or non-const reference to the geometry's first and last point, instead of returning a copy of the point.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-06-27 10:30:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/78#issuecomment-47329450  

What if we wanted to add such functionality related to other iterators? Could some kind of universal tool be implemented for this? Or a pattern at least.  
IMHO we should avoid adding such functions to the namespace bg, this could cause a various names bloat in the future.  
Furthermore the functionality implemented in namespace bg::range is duplicated.  
On the other hand I understand its purpose and don't have a solution which solves all problems.  
  
My first idea was to reuse the existing range::front() and range::back(). But to use them we'd need to somehow return a specific range from geometry, e.g.:  
  
```  
bg::points_range<Geometry> rng = bg::points(geometry);  
bg::range::front(rng);  
```  
  
But since this also complicates the interface, maybe a view would be better?  
  
```  
bg::view::points<Geometry> rng(geometry);  
bg::range::front(rng);  
  
bg::range::front(bg::view::make_points(geometry));  
```  
  
Of course the names can be changed and make_xxx is not needed (we don't have such functions now).  
This also complicates the interface, but not the functionalities gathered in the main namespace, and it doesn't duplicate the functionality. And it allows to use the functionality from Boost.Range.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-06-27 10:36:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/78#issuecomment-47329789  

@awulkiew In principle I agree with you: functionality should not be duplicated.  
My problem here is with semantics: the point iterator does not point to a range; it is merely a bidirectional iterator. In that sense I would really like to avoid using (or suggest using) functionality from bg::range namespace.  
  
Implementing a points_view seems a much better idea. On the other hand the semantics problem is still there: the point iterator does not point to a range.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-06-27 10:57:48 UTC  
> Updated_at: 2014-06-27 10:58:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/78#issuecomment-47331183  

The ranges reflects the iterators 1:1. In this case two bidirectional iterators may define a bidirectional range.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-06-27 11:17:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/78#issuecomment-47332391  

@awulkiew Okay, I get it.  
I do not have a particular strong prefence right now for the two options you suggested. I would like to think more about it and hear other opinions as well. If I were to choose now I would go for implementing bg::points_range.  
In any case I do agree that there is really no need to duplicate functionality. So from my standpoint I am okay with removing points_front points_back.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2014-06-27 18:54:52 UTC  
> Updated_at: 2014-06-27 18:55:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/78#issuecomment-47387211  

Agreed - let's remove point_front and point_back. They are in the namespace boost::geometry but should not be used - edit: I mean, if we release this, we cannot remove them later.  
  
Actually I did not know of their existance. I see that it is added May 5 but I don't see mail about this in my box. Maybe I missed it.  
  
I like the iterators (point_begin, point_end) but copying range functionality there is a bit too much, we should take the range approach then. So agreed.  
  
We can implement the range later, no need to do it now.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-06-30 08:18:26 UTC  
> Updated_at: 2014-06-30 08:18:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/78#issuecomment-47505542  

@barendgehrels I will remove points_front and point_back. I will create another pull request for that.  
  
I remember mentioning points_front/points_back when I added the bidirectional support. I do not remember specific dates. I can search my mailbox if needed.

---
