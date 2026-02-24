# #315 Fix: possible infinite loop in has_spikes [Merged]

> Username: mkaravel  
> Created at: 2015-07-09 09:01:33 UTC  
> Updated at: 2015-07-14 20:46:36 UTC  
> Merged at: 2015-07-14 17:44:19 UTC  
> Closed at: 2015-07-14 17:44:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/315  

If the range passed to `has_spikes` consists of points with `NaN` coordinates (this can happen if the range passed as been computed as the result, e.g., of a set operation), then `has_spikes` can enter an infinite loop.  
  
If a point `p` has such `NaN` coordinates, then `p` is considered as not equal to itself, a property/feature assumed in the previous implementation of `has_spikes`), and the following calls (in the older version of `has_spikes`) to `std::find_if` never return:  
  
```  
std::find_if(prev, boost::end(view), not_equal(*prev));  
```  
  
and  
  
```  
std::find_if(cur, boost::end(view), not_equal(*cur));  
```  
  
The solution if to essentially call `std::find_if` as `std::find_if(second, first, last, not_equal(*first))`, which avoids the implicit assumption that the `bg::equals(*first, *first)` returns `true`.  
  
**_Note:**_ the problem appears for geometries with `NaN` coordinates. The point addressed here is to have `has_spikes` return instead of entering an endless loop; the result returned, however, does not (and could not) have any geometric significance.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-07-09 21:44:25 UTC  
> Updated_at: 2015-07-14 17:42:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#discussion_r34307421  

BOOST_GEOMETRY_ASSERT ?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-07-09 21:46:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-120153107  

Can you add a unit-test for this situation?  
If Adam is also OK I'm OK with merging

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-07-09 23:07:54 UTC  
> Updated_at: 2015-07-14 17:42:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#discussion_r34314262  

You are absolutely right. Will change it.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-07-09 23:08:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-120171261  

I had in mind to add a unit test. Will do before merging (of course I am also waiting for Adam's approval).

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-07-10 04:41:20 UTC  
> Updated_at: 2015-07-14 17:42:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#discussion_r34327280  

Done. See commit 78296da.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-07-10 04:41:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-120222997  

Test cases for `bg::is_simple` and `bg::is_valid` added. See commit 73960f3.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-07-10 12:25:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-120398785  

So basically the algorithm now starts searching from the next element. However I find the way how `find_not_equal` works slightly confusing because it takes a Range but ommits the first element. Wouldn't it be more clear to "manually" increment the iterator in the main algorithm and then use `find_if` directly?  
I'm of course ok with merging.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-07-13 03:46:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-120802429  

I wrote this method in order to avoid code duplication. What should be done in each case is really what the method does. If you prefer another name for the method, I am okay. I do not see the point though, of removing the method and duplicating the code.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-07-13 13:48:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-120933466  

Yes, I understand that your goal was to avoid duplication. Ok, let's try to find a better name for it. We're searching for one element so a function should indeed contain a word _find_. E.g. `find_mismatch` is one possibility but it's not clear what's this _mismatch_, which elements are compared exactly. So maybe something like `skipping_find` (name similar to `adjacent_find`) or rather `skipping_find_if` skipping some number of elements (1 in this case). But then, something like `skipping_iterator` could be used with standard `find_if` instead, though I think there is no such thing in Boost. Well, `filter_iterator` could probably be used but the code would probably be more confusing.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2015-07-13 19:20:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-121028414  

How about `find_different_from_first`? Would that work for you?

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-07-14 09:29:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-121179404  

A little too descriptive for my taste but indeed more clear, so ok.

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-07-14 17:43:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-121320003  

Done: method `find_not_equal` renamed to `find_different_from_first`.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-07-14 20:46:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/315#issuecomment-121379642  

Thanks!

---
