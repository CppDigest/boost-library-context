# #1247 Use if constexpr macro instead of condition macro. [Merged]

> Username: awulkiew  
> Created at: 2024-02-24 21:31:44 UTC  
> Updated at: 2024-04-01 11:29:21 UTC  
> Merged at: 2024-02-29 21:58:35 UTC  
> Closed at: 2024-02-29 21:58:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247  

This PR replaces uses of `BOOST_GEOMETRY_CONDITION` with `BOOST_GEOMETRY_CONSTEXPR` if possible and it is not destructive to readability.  
  
Unfortunately it as less straightforward than I thought it would be because with MSVC and level 4 warnings enabled "unreachable code" warning is generated of some code exists outside `if constexpr` block. In such case an additional `else` block has to be added to prevent the warning. This means there are more blocks and indentations.  
  
Constexpr conditions has to be checked separately from regular conditions. This means that some of the code has to be repeated e.g. if it has to be executed for both compile-time or run-time condition. This also can add more blocks and indentations.  
  
For the reasons above I left `BOOST_GEOMETRY_CONDITION` in formulas because code like this:  
```  
if (Foo == 0)  
{  
   return bar;  
}  
// update bar  
if (Foo == 1)  
{  
   return bar;  
}  
// update bar  
etc.  
```  
would force me to add `else` brackets. Unless we agreed to write `if constexpr` differently than "regular" `if`s, with different brackets placement, indentations, etc. I can't think of any way that would look good.  
  
So if you think that some of the changes look bad feel free to point it out so I'll revert it to `BOOST_GEOMETRY_CONDITION`.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-25 10:52:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1247#pullrequestreview-1899651169  

📁 include/boost/geometry/algorithms/detail/overlay/append_no_dups_or_spikes.hpp

```diff
   1 | // Boost.Geometry (aka GGL, Generic Geometry Library)
   2 | 
   3 | // Copyright (c) 2007-2012 Barend Gehrels, Amsterdam, the Netherlands.
```

> Username: barendgehrels  
> Created_at: 2024-02-25 10:52:57 UTC  
> Updated_at: 2024-02-25 11:02:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247#discussion_r1501794473  

-> `2024`  
  
(it's about the next line, but you can ignore this comment)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-25 10:57:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1247#pullrequestreview-1899651841  

📁 include/boost/geometry/util/precise_math.hpp

```diff
 459 |       + A_33 * (A_11_x_A_22[0] - A_21_x_A_12[0]);
 460 |-     if(Robustness == 0) return det;
 460 |+     if (BOOST_GEOMETRY_CONDITION(Robustness == 0))
```

> Username: barendgehrels  
> Created_at: 2024-02-25 10:57:47 UTC  
> Updated_at: 2024-02-25 11:01:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247#discussion_r1501795307  

~~Can this (and similar) made `CONSTEXPR` as well? Apart from needing the `else` branch, but you did that elsewhere too~~  
Scratch that, it was already in the description of the PR


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2024-02-25 11:00:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1247#pullrequestreview-1899652203  

I'm OK with this change.  
  
I made a remark about the copyright (sometimes 2023, sometimes 2024) but it's not important, and maybe you started it already last year.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2024-02-25 11:02:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247#issuecomment-1962894931  

Thanks for the change

---

## Comment 5

> Username: awulkiew  
> Created_at: 2024-02-25 12:19:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247#issuecomment-1962918571  

> I made a remark about the copyright (sometimes 2023, sometimes 2024) but it's not important, and maybe you started it already last year.  
  
Yes, I made the majority of changes last year and came back to it now. I was forced to resolve some conflicts so I updated the dates accordingly.

---

## Review 6 [Commented]

> Username: vissarion  
> Created_at: 2024-02-26 10:04:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1247#pullrequestreview-1900434911  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_ll.hpp

```diff
 599 |         {
 591 |-             if ( BOOST_GEOMETRY_CONDITION(is_version_touches) )
 600 |+             bool output_spike = false;
```

> Username: vissarion  
> Created_at: 2024-02-26 10:02:11 UTC  
> Updated_at: 2024-02-26 10:04:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247#discussion_r1502336157  

this is also change the behavior e.g. the block   
```  
            tp.operations[0].operation = operation_intersection;  
            tp.operations[1].operation = operation_blocked;  
            *out++ = tp;  
            //tp.operations[0].operation = operation_intersection;  
            tp.operations[1].operation = operation_intersection;  
            *out++ = tp;  
```  
was executed in all cases but now only in touch or append_colinnear_opposite cases.   
  
Is this intentional?

> Username: awulkiew  
> Created_at: 2024-02-28 19:22:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247#discussion_r1506469788  

If there is any change in behavior it was not intentional. But AFAIU there is no change here. We're talking about the block below at line 607(old) or 622(new) correct? In the old version it was executed:  
```  
if ( is_q_spike  
  && ( BOOST_GEOMETRY_CONDITION(is_version_touches)  
    || inters.d_info().arrival[1] == 1 ) )  
```  
now it's executed if `output_spike` is `true` and this is set in block:  
```  
if (is_q_spike)  
```  
and only  
```  
if BOOST_GEOMETRY_CONSTEXPR (is_version_touches)  
```  
or  
```  
else if (inters.d_info().arrival[1] == 1) // Version == append_collinear_opposite  
```  
  
For convenience here is [the old version](https://github.com/boostorg/geometry/blob/eb38231d367fe0b74fe8c2b4083d94870a86ac9d/include/boost/geometry/algorithms/detail/overlay/get_turn_info_ll.hpp) and [the new version](https://github.com/boostorg/geometry/blob/f25545b58e54857f56cfe90769d9bdafe4bca731/include/boost/geometry/algorithms/detail/overlay/get_turn_info_ll.hpp).  
  
Or am I missing something?

> Username: vissarion  
> Created_at: 2024-02-29 07:11:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247#discussion_r1507107474  

OK, I was looking only at the changes and thought you were fixing something. It is clear that you just rewrote it, and it is actually simpler this way. So, everything is fine here.


---

## Review 7 [Approved]

> Username: vissarion  
> Created_at: 2024-02-29 07:14:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1247#pullrequestreview-1908118957  

LGTM, thanks!

📁 include/boost/geometry/algorithms/detail/relate/point_point.hpp

```diff
  71 | 
  72 |- 
  72 |+ 
```

> Username: vissarion  
> Created_at: 2024-02-29 07:12:13 UTC  
> Updated_at: 2024-02-29 07:14:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247#discussion_r1507108498  

this is probably here by mistake.

> Username: awulkiew  
> Created_at: 2024-02-29 20:53:47 UTC  
> Updated_at: 2024-02-29 20:53:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1247#discussion_r1508182115  

yes, fixed


---
