# #1086 [turn] fix distance measure for equal points [Merged]

> Username: barendgehrels  
> Created at: 2022-11-09 12:04:19 UTC  
> Updated at: 2022-11-17 15:09:29 UTC  
> Merged at: 2022-11-16 13:00:44 UTC  
> Closed at: 2022-11-16 13:00:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086  

Fixes: #1076

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-11-09 12:04:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1086#pullrequestreview-1173910830  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
  93 |         1, 5, 1.0);
  94 | 
  95 |- #if defined(BOOST_GEOMETRY_USE_RESCALING) || defined(BOOST_GEOMETRY_TEST_FAILURES)
```

> Username: barendgehrels  
> Created_at: 2022-11-09 12:04:52 UTC  
> Updated_at: 2022-11-09 12:04:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1017849849  

This was already fine now, it is not fixed by this particular fix

> Username: vissarion  
> Created_at: 2022-11-09 12:26:04 UTC  
> Updated_at: 2022-11-09 12:26:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1017870891  

Sure, but this comment will be lost in the history of commits. Is it better to add this changes in a separate commit? Also applies to other tests in this PR.

> Username: barendgehrels  
> Created_at: 2022-11-16 09:14:58 UTC  
> Updated_at: 2022-11-16 09:14:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1023721728  

:+1: I'll do

> Username: barendgehrels  
> Created_at: 2022-11-16 09:20:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1023727641  

:heavy_check_mark: done, thanks


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2022-11-09 12:05:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1086#pullrequestreview-1173911624  

📁 test/algorithms/buffer/buffer_linestring.cpp

```diff
 325 |+         using bg::strategy::buffer::join_round;
 326 |+         using bg::strategy::buffer::end_round;
 327 |+         test_one<linestring, polygon>("issue_1084", issue_1084, join_round(10), end_round(10), 13200.83, 10.0);
```

> Username: barendgehrels  
> Created_at: 2022-11-09 12:05:28 UTC  
> Updated_at: 2022-11-09 12:05:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1017850334  

In this PR also the test for #1084 which runs fine on 1.80


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2022-11-09 12:26:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1086#pullrequestreview-1173938350  

I am OK, thanks!

📁 include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp

```diff
 134 |+     if (identical(p1, p) || identical(p2, p))
 135 |+     {
 136 |+         detail::distance_measure<calc_t> const result;
```

> Username: vissarion  
> Created_at: 2022-11-09 12:23:18 UTC  
> Updated_at: 2022-11-09 12:26:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1017868207  

is it safer and cleaner to set this explicitly to `0`?

> Username: barendgehrels  
> Created_at: 2022-11-16 09:14:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1023721193  

It's done in the constructor of `distance_measure`

> Username: vissarion  
> Created_at: 2022-11-16 11:02:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1023848530  

I know, I just comment that it is more clear to do explicit initialization so the reader will not have to see the declaration of the constructor, but that is my own preference. So I am resolving the comment :)

> Username: barendgehrels  
> Created_at: 2022-11-16 11:21:27 UTC  
> Updated_at: 2022-11-16 11:21:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1023867391  

Thanks. Agree it's more clear, but it's internal code and takes (a minimum amount) of time...


---

## Comment 4

> Username: vissarion  
> Created_at: 2022-11-16 11:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#issuecomment-1316844048  

@barendgehrels do you want this to be in `1.81`?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2022-11-16 11:35:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#issuecomment-1316856252  

> @barendgehrels do you want this to be in `1.81`?  
  
That would be nice. Is it possible? I can merge it now.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2022-11-16 11:36:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#issuecomment-1316857341  

> > @barendgehrels do you want this to be in `1.81`?  
>   
> That would be nice. Is it possible? I can merge it now.  
  
Then I'll remove the doc-part about geographic buffer from this PR.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2022-11-16 12:43:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#issuecomment-1316963247  

> > > @barendgehrels do you want this to be in `1.81`?  
> >   
> >   
> > That would be nice. Is it possible? I can merge it now.  
>   
> Then I'll remove the doc-part about geographic buffer from this PR.  
  
:heavy_check_mark: done

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2022-11-16 12:44:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#issuecomment-1316964052  

> > > > @barendgehrels do you want this to be in `1.81`?  
> > >   
> > >   
> > > That would be nice. Is it possible? I can merge it now.  
> >   
> >   
> > Then I'll remove the doc-part about geographic buffer from this PR.  
>   
> heavy_check_mark done  
  
I'll merge it as soon as the checks are green. @awulkiew are you fine with this?

---

## Review 9 [Commented]

> Username: barendgehrels  
> Created_at: 2022-11-16 12:45:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1086#pullrequestreview-1182502247  

📁 doc/release_notes.qbk

```diff
  27 | 
  28 |- * [@https://github.com/boostorg/geometry/pull/1045 1045] Support geographic buffer for linestrings (side, joins)
  28 |+ * [@https://github.com/boostorg/geometry/issues/1076 1076] Union: in rare cases it might miss one polygon
```

> Username: barendgehrels  
> Created_at: 2022-11-16 12:45:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1086#discussion_r1023948374  

@vissarion if you cherry-pick this commit (together with the other 2), then it should be fine.


---
