# #531 Feature/get turn info add retrieve policy [Merged]

> Username: barendgehrels  
> Created at: 2018-11-28 20:56:27 UTC  
> Updated at: 2021-06-02 09:48:11 UTC  
> Merged at: 2018-12-14 21:46:48 UTC  
> Closed at: 2018-12-14 21:46:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/531  

This PR (actually my first) adds a retrieve policy, and passes it to get_turn_info.  
  
This policy retrieves a point known as "k" (pk / qk), the first non-duplicate point coming consecutively after the points "i" and "j" (two points from two segments p and q, part of the intersection of two segments, so these points are pi,pj and qi,qj).  
  
In the past points pk/qk were just passed. But now we need another point (pl/ql) and it is not convenient to add that. Therefore the policy is introduced.  
  
An additional advantage is that point "k" is now retrieved lazily, on demand. Often that point is not necessary. It is actually only necessary if there are collinearies. And often that is not the case. So in all those cases, performance should increase just a little bit.  
  
This PR adds the policy and retrieves point "k" using the method .get(). In another PR I will add an argument to get, to be able to retrieve also the subsequente point, as described above. But for now this PR is large enough.  
  
It is also related to side calculation, the place where that point is used, to check whether it is located left/right or collinear to a segment.  
  
To my surprise, also non-typical usage of side calculation were introduced in the implementations for linear intersections, and the typical order is changed there. Therefore I copied the originals there - they cannot use the policy because they don't use the "k" point. Actually there should be looked at, because it is looking suspicious, but we can do that afterwards.  
  
I'm curious to your findings.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-11-29 01:51:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-442675718  

Interesting. Regarding future plans, what for will be additional points `pl` and `ql` needed?  
  
The PR looks good to me. I'm only wondering why you've made certain naming decisions:  
- The name of the policy. Would it be possible to rename it to know what is retrieved exactly? What is it for on a higher level of abstraction? What is stored and why? AFAIU it's for optional, additional data of an intersection segment: next point and flags for the current segment (is first or last segment). Or are the flags for first/last point? Is that correct? `OptionalPointDataPolicy`, `AdditionalSidePointPolicy`, `OptionalSegmentInfoPolicy`etc.?  
- The name of function `get()` may be confusing because it's not clear what is it returning exactly. Furthermore we use `get()` for coordinates. Why not `get_point()` or even explicitly `get_point_k()` (and later `get_point_q()`)?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2018-11-29 03:25:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-442693073  

Do you want to release it with 1.69 or is this a mistake? At this point we can push only bugfixes (if permission is granted) and documentation updates.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2018-11-29 07:17:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-442731121  

Ah, sorry I meant 1.70  
No need now  
Thanks

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2018-11-29 07:18:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-442731290  

Will react tonight on your suggestions

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2018-11-29 21:19:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-442996815  

Thanks for your comments.  
  
I can make it get_point_k, that is clear (as long as people understand the meaning of "k")...  
W.r.t. the name: it retrieves extra information about the segments being intersected. For both segments there is an independent policy (might well be different type, for example for box/poly).  
  
Basic information, per segment, is   
- point i  
- point j  
They form together the segment. This is not retrieved by the policy (so indeed the policy gets additional information).  
  
The extra information is:  
- is_first() -> returns true if i-j is the first segment (only for linear features)  
- is_last() -> returns true if i-j is the last segment (only for linear features)  
In the last case, there is no point "k"! So maybe (did not think of that earlier) get_point_k should return a pointer (might be nullptr) or an optional (we're not yet dependent on boost optional). Or just as it is now, but its contents should be considered as undefined if is_last == true  
- get_point_k() -> returns the first spatially unequal (at least, that should - for some operations that is not yet done) point after j  
- get_point_l() -> returns the second spatially unequal point -> and also here it might be (for linear features) we need information about if it does exist, or not.  
  
Maybe it is the best to return it as a pointer? Or return a boolean (and copy the point)? Or an extra method (has_k has_l)? All possible.  
  
Will I rename the policy to retrieve_additional_info_policy ? (you did not suggest this one yet, but I think it is clear). Or optional_info_policy? It is about intersecting segments, that is known by context, and it is not always about side so that does not have to be part of the name I think.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2018-12-01 10:30:07 UTC  
> Updated_at: 2018-12-01 10:44:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443416360  

~~Actually - get_point_k() is quite specific, isn't it?  
I now consider Point* get_point(int index), where index=2 (for point k) and index=3(for point l). This means index 0 and 1 (for i and j), but they are not used. I think that will be most intuitive. What do you think?~~  
  
Scratch all that. I'm applying it but the pointer syntax is not nice, it introduces ifs everywhere. I think it is better to check once (as done now, using is_last) and guarantee a point is returned.  
And the point is assigned to pk or qk everywhere, so I'm OK with get_point_k()

---

## Comment 7

> Username: awulkiew  
> Created_at: 2018-12-01 11:21:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443419220  

The names you propose are better then the original for sure but I'm wondering if we could find something even more descriptive.  
  
This is additional info for segment (2 points), or 3-4 non-equal points in a geometry. So a relevant section/fragment/part or a geometry. Thought this is not a representation of the data stored in a geometry because if some of the points are equal some of them may be skipped. So this is actually a cherry-picked/newly-formed fragment of a line. Do you have an idea how something like this could be named?  
  
I like the explicit `get_point_k()` better I think.  
  
How about internally storing pointers to points and return references? This way you wouldn't be forced to store a separate `bool` state, this type would behave like a reference type and `mutable` wouldn't be needed.  
  
Have you considered passing only the policies and storing also points `i` and `j` inside? This way the policy wouldn't be "optional" anymore. It'd become something like `FragmentPolicy`, or `ReferringLinePolicy`.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2018-12-01 12:07:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443421649  

Internally (in get_turns.hpp) we call them nonduplicate points. So then it would be "get_non_duplicate_point_k" - which is quite long... For me it can be omitted there.  
  
I will think about storing a pointer internally - it is then basically up to the policy itself. For example a box is always circular, will never return true there, so there it is not necessary.  
  
Renaming is_last to "has_k" and reversing the meaning is probably convenient. Because then we can add "has_l" for "get_point_l".  
  
I indeed have thought about passing policies with i/j inside. However, that is never necessary, it is really about intersecting a segment, and a segment consists of i/j which are never optional. Therefore I did not do that, I don't want to make too much changes for the sake of the changes themselves now...

---

## Comment 9

> Username: awulkiew  
> Created_at: 2018-12-01 13:24:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443426079  

I mentioned passing points i/j because then semantically the policy would be expressed better since it'd basically become a `referring_linestring`.  
  
However the problem with pointers is that geometries may use different `coordinate_type` and we potentially have to create points with `calculation_type` coordinates. So pointers would only be good if required points were always created at some higher level. If they are not, and are created only when access is required then this is not a good solution.  
  
I think the names of functions are ok. I'd only think about the name of the policy. If it is descriptive then function names will be obvious. A few more names: `CurveFragmentPolicy` `AdditionalRangePolicy` `OptionalCurveInfo`.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2018-12-02 17:29:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443524912  

Hi @awulkiew , I did a small begin to check your idea of using retrieve policy instead of passing pi/pj and qi/qj . That actually starts to look quite good, so if you agree, will continue with that and update this PR. Expected coming Wednesday. Yes, then it is a retrieve_policy again, we can then omit the additional info.  
Thanks.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2018-12-02 18:02:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443527829  

@barendgehrels Of course I agree. ;) Ok, so the policy would represent a segment of potentially newly-created points (intersection point can be one of them) and possibly additional info like flags, next non-equal point in the new geometry and yet another point.  
  
So I have another bunch of possible names describing that this is a new fragment of geometry created by intersecting some geometries: `SubRangePolicy`, `NewSubRingPolicy`, `NewRingPartPolicy`, `IntersectionCurvePolicy`, `IntersectionFragmentPolicy`. Btw, when I'm proposing these names I don't expect you to use them exactly. It's partly a way of expressing abstractions I'm thinking about.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2018-12-02 18:10:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443528977  

(Unique)SubRangePolicy sounds good to me.  
Actually I don't understand your remark about "created by intersecting" - it is getting unique points from an existing geometry, and passes it.  
They are later intersected indeed, but that is not related at all to this retrieval - do I miss something?

---

## Comment 13

> Username: awulkiew  
> Created_at: 2018-12-02 18:28:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443530604  

@barendgehrels No, I think I was not getting it. I thought it represents a sub-ring after intersection but this is simply a sub-range of non-equal points (after checks for equality). So this is not newly-created neither intersection result at this point. And AFAIU it represents only points from 1 geometry. Is that correct? If it is then all of the above remarks about storing intersection point or points of various types in one policy are incorrect. So it's simpler than I thought and pointers could indeed still be used internally which would make the policy something more like a `referring_linestring`, at least for non-Boxes. Correct?

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2018-12-02 21:50:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443545426  

Right, that explains indeed some other things I did not understand ;-)  
OK, indeed it represents points from only one geometry - so I think referring_linestring or referring_ring (because it is circular) are good descriptions - maybe referring_range because that is more neutral.  
I can continue with that, thanks for your input!

---

## Review 15 [Commented]

> Username: vissarion  
> Created_at: 2018-12-03 11:07:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-180713586  

This PR looks interesting to me too!  
  
Regarding naming I had to read the whole thread to understand exactly what is going on.  https://github.com/boostorg/geometry/pull/531#issuecomment-442996815 is very descriptive, thanks!  
  
However, the use of indices `i,j,k,l` to name four successive (modulo duplicates) points took me a while to digest it. Apart from being successive letters in latin alphabet there is no other clue why those points should be successive. Maybe naming them `a,b,c,d`, or `one,two,three,four`, or even indexing by `1..4` would be more clear. But this is a matter of taste.  
  
+1 for the rest.

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2018-12-03 17:26:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-443794550  

@vissarion  thanks for your review!  
Right, based on the suggestion of @awulkiew to also pass points pi etc, and use a referring_ring-like concept, I will change the i,j,k,l into indices. I think indeed that is more clear.  
I took i/j/k because they are always used as such in get_turn_info, but at the point they are defined, that is not know and actually also not that relevant.  
So thanks for your remark, it convinces more that it should be changed a bit.

---

## Comment 17

> Username: barendgehrels  
> Created_at: 2018-12-04 19:53:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-444235052  

Hi @awulkiew  and @vissarion , it is now revised based on your remarks, and quite nice I think.  
So retrieve_policy is now unique_sub_range and has a subset of range concepts (only "at" and "size" replacing get_point_X and has_p). We pass a lot less now...  
Curious to your findings.

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2018-12-05 10:38:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-444439531  

Some more changes, especially removed apply from AssignPolicy because it was not used anymore (buffer did use it until yesterday but that is now solved differently - and will go with rescaling).  
I believe it is ready now, running all tests again.

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2018-12-05 13:45:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-444489983  

All tests pass - if you agree then it can be merged

---

## Comment 20

> Username: awulkiew  
> Created_at: 2018-12-05 13:53:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-444492297  

Great, I'll review it today. Btw, did you check what's the difference in performance, if any?

---

## Comment 21

> Username: barendgehrels  
> Created_at: 2018-12-05 13:56:49 UTC  
> Updated_at: 2018-12-05 15:07:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-444493461  

I'll do a quick check on performance. If any it should be a bit faster because the points known as "k" are now retrieved lazily, and not always used, so that should save some time. On the other hand some points on buffer are now rescaled non-lazily, but that occurs less (I would think).

---

## Comment 22

> Username: barendgehrels  
> Created_at: 2018-12-05 15:06:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-444517177  

Performance is increased a bit:  
  
**Union/intersection:**  
$ ./recursive_polygons --seed 1234 --level 7 --size 20 --count 100  
old: polygons: 25500 type: d time: 6.91922  
new: polygons: 25500 type: d time: 6.54316  
  
**Buffer:**  
$ ./recursive_polygons_buffer --seed 1234 --level 7 --count 100  
old: geometries: 25500 type: d time: 9.22374  
new: geometries: 25500 type: d time: 9.13735

---

## Review 23 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 02:39:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182063756  

📁 include/boost/geometry/algorithms/detail/buffer/get_piece_turns.hpp

```diff
  60 |+             case 1 : return m_point_j;
  61 |+             case 2 : return get_next_point();
  62 |+             default : return m_point_i;
```

> Username: awulkiew  
> Created_at: 2018-12-06 02:39:44 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239311578  

Why not have an assertion here? Or is it a valid case?


---

## Review 24 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 02:43:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182064380  

📁 include/boost/geometry/algorithms/detail/buffer/get_piece_turns.hpp

```diff
 103 |+     Ring const& m_ring;
 104 |+     point_type m_point_i;
 105 |+     point_type m_point_j;
```

> Username: awulkiew  
> Created_at: 2018-12-06 02:43:02 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239312095  

I'm wondering, why not have iterators here as well to avoid copying? Ar are the points copied because you fear cache misses?


---

## Review 25 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 02:44:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182064626  

📁 include/boost/geometry/algorithms/detail/buffer/get_piece_turns.hpp

```diff
 106 |+     iterator_type m_iterator;
 107 |+ 
 108 |+     mutable point_type m_point;
```

> Username: awulkiew  
> Created_at: 2018-12-06 02:44:31 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239312306  

Right, the same here. Is there a reason why simply the iterator isn't set to point to the relevant point in a range and left there instead of copying the point here?


---

## Review 26 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 02:48:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182065220  

📁 include/boost/geometry/algorithms/detail/buffer/get_piece_turns.hpp

```diff
 261 |-                                     m_robust_policy,
 262 |-                                     std::back_inserter(m_turns));
 283 |+                 turn_policy::apply(the_model,
```

> Username: awulkiew  
> Created_at: 2018-12-06 02:48:10 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239312793  

Is there a reason why the order of arguments is changed? Previously the points were passed as the first arguments and after that strategies etc. Not that this is a problem, just pointing it out.


---

## Review 27 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 02:48:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182065335  

📁 include/boost/geometry/algorithms/detail/overlay/get_intersection_points.hpp

```diff
  50 |                 Strategy const& strategy,
  51 |+                 UniqueSubRange1 const& range_p,
  52 |+                 UniqueSubRange2 const& range_q,
```

> Username: awulkiew  
> Created_at: 2018-12-06 02:48:57 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239312886  

Same here. The input ranges are passed after the strategy.


---

## Review 28 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 02:50:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182065515  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 519 |+     static inline void apply(
 520 |+                 UniqueSubRange1 const& range_p,
 521 |+                 UniqueSubRange2 const& range_q,
```

> Username: awulkiew  
> Created_at: 2018-12-06 02:50:05 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239313048  

Here they are passed at the beginning of arguments list which is as it was before.


---

## Review 29 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 02:57:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182066675  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_for_endpoint.hpp

```diff
 312 |-         bool is_q_last_ip = is_q_last && ip_info.is_qj;
 335 |+         bool is_p_first_ip = range_p.is_first() && ip_info.is_pi;
 336 |+         bool is_p_last_ip = range_p.size() == 2u && ip_info.is_pj;
```

> Username: awulkiew  
> Created_at: 2018-12-06 02:57:07 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239314027  

This condition looks non-intuitively. Is it based on an assumption that for last segment the point `k` is not retrieved? This way here, the caller has to know how the `range_p` and `range_q` below are filled possibly in some other place of the code. Would it be possible to have and explicit function `is_last()` (like `is_first()`) so here it'd be clear what's happening and all details are hidden in the `range_p`?


---

## Review 30 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 02:58:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182066963  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_helpers.hpp

```diff
 105 |+             case 1 : return m_robust_point_j;
 106 |+             case 2 : return get_point_k();
 107 |+             default : return m_robust_point_i;
```

> Username: awulkiew  
> Created_at: 2018-12-06 02:58:39 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239314271  

Why not an assertion?


---

## Review 31 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:02:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182067526  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_helpers.hpp

```diff
  76 |+     SideStrategy m_side_strategy; // NOTE: cannot be const&
  77 |+     UniqueSubRange1 const& m_range_p;
  78 |+     UniqueSubRange2 const& m_range_q;
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:02:06 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239314696  

So here `UniqueSubRange1` and `UniqueSubRange2` are `const&`, yet `SideStrategy ` is not. Are we sure that ranges will live as long as the side_calculator object? Are we sure that they will not be created from temporaries like:  
  
    side_calculator sc(some_range1(), some_range2(), get_side_strategy());  
  
because if they are created like that these references will be dangling.


---

## Review 32 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:03:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182067785  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_helpers.hpp

```diff
  67 |-     Qk const& m_qk;
 125 |+     Point const& m_robust_point_i;
 126 |+     Point const& m_robust_point_j;
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:03:44 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239314900  

So here robust points are stored as references, are we sure that whatever is passed to the constructor of `robust_subrange_adapter` will live longer than an object of this class?


---

## Review 33 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:08:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182068562  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_helpers.hpp

```diff
 405 |     inline bool is_spike_p() const
 406 |     {
 407 |+         if (base::m_range_p.size() == 2u)
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:08:36 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239315554  

Out of curiosity, in which case something like this can happen? Is this a check for invalid input or a check for last segment as it was before?


---

## Review 34 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:09:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182068752  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_helpers.hpp

```diff
 442 |     inline bool is_spike_q() const
 443 |     {
 444 |+         if (base::m_range_q.size() == 2u)
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:09:56 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239315728  

Right, here it is too. Is the reasoning that if `base::m_range_q.size() == 2u` then there is no `base::rqk()`?


---

## Review 35 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:11:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182068996  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_la.hpp

```diff
 697 |+                                            inters.result(),
 698 |+                                            range_p.size() == 2u,
 699 |+                                            range_q.size() == 2u);
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:11:32 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239315945  

This is non-intuitive. A person reading it 6 months from now will ask "why 2 exactly"? ;)


---

## Review 36 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:12:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182069102  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_la.hpp

```diff
 800 |         if ( BOOST_GEOMETRY_CONDITION(EnableLast)
 830 |-           && is_p_last
 801 |+           && range_p.size() == 2u
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:12:17 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239316039  

Here.


---

## Review 37 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:12:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182069180  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_ll.hpp

```diff
 451 |-                     else if ( is_p_last
 452 |-                            && equals::equals_point_point(pj, tp.point) )
 411 |+                     else if ( range_p.size() == 2u
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:12:50 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239316103  

Here.


---

## Review 38 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:12:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182069202  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_ll.hpp

```diff
 461 |-                     else if ( is_q_last
 462 |-                            && equals::equals_point_point(qj, tp.point) )
 421 |+                     else if ( range_q.size() == 2u
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:12:58 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239316122  

Here.


---

## Review 39 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:16:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182069754  

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 136 |+     {
 137 |+         return IsAreal ? 3
 138 |+             : m_section.is_non_duplicate_last && m_index + 1 >= m_section.end_index ? 2 : 3;
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:16:23 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239316578  

So this is the cause of the `2` condition above. Right? ;)


---

## Review 40 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:16:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182069813  

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 146 |+             case 1 : return m_current_point;
 147 |+             case 2 : return get_next_point();
 148 |+             default : return m_previous_point;
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:16:50 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239316624  

Possible assertion.


---

## Review 41 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:18:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182070002  

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 194 |+     signed_size_type m_index;
 195 |+     Point const& m_previous_point;
 196 |+     Point const& m_current_point;
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:18:10 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239316795  

And here points are stored as references. What is the reason behind the inconsistency?


---

## Review 42 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:20:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182070389  

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 594 |+     typedef typename geometry::point_type<Range>::type range_point_type;
 595 |     typedef typename geometry::point_type<Box>::type box_point_type;
 596 |+     typedef boost::array<box_point_type, 4> box_array;
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:20:40 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239317135  

Boost.Array requires that the stored type has the default constrictor. Just pointing it out, there are probably many default-constructed points in the code. Still AFAIK this requirement is not a part of the Point concept.


---

## Review 43 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-06 03:22:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-182070683  

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 559 |-         boost::array<box_point_type,4> bp;
 560 |-         assign_box_corners_oriented<ReverseBox>(box, bp);
 692 |+         box_array box_points;
```

> Username: awulkiew  
> Created_at: 2018-12-06 03:22:20 UTC  
> Updated_at: 2018-12-08 20:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r239317370  

Right, here Boost.Array was used before so this is not a change.


---

## Comment 44

> Username: awulkiew  
> Created_at: 2018-12-06 03:30:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-444736350  

Thanks! It's great that it's also faster.  
  
In general I'm ok with merging. I see only 1 issue I'd suggest you changed, i.e. `is_last()` instead of `size() == 2`. Maybe also the order of arguments of some functions.  
  
The other things are minor issues and questions. E.g. I don't know if the switch-cases without assertions are needed as they are or not. I'm ok with them and other things like keeping references or copies in various cases if you prefer to keep them as they are. But I'm interested in knowing if there is a key to when points are stored in one or the other way.

---

## Comment 45

> Username: barendgehrels  
> Created_at: 2018-12-07 18:18:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-445319658  

Hi @awulkiew , thanks (again) for your review.  
About is_last(), I understand, it makes sense. The only objection I have is that it is a bit ambiguous. Because it does not say which is last (i-j ? j-k ? k-l?).   
I can make it "is_last_segment()", which is maybe a bit more clear especially if we document that unique_sub_range is all about one segment and one or two following points.  
  
About the order: I agree, will change. Cause was actually that I first made it a policy, and only later renamed it but did not move it.  
About the assertions: fine for me, will add them. And I will also look at the references.

---

## Comment 46

> Username: barendgehrels  
> Created_at: 2018-12-07 18:31:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-445323378  

And about is_spike_p / is_spike_q, I did not introduce them, but they all had a condition !is_p_last or !is_q_last before. That made sense because if p or q is last, then it can never be a spike and basically point k should not be accessed. Therefore I moved that condition inside.  
So indeed, then there is no base::rqk()

---

## Comment 47

> Username: barendgehrels  
> Created_at: 2018-12-07 19:45:18 UTC  
> Updated_at: 2018-12-08 11:02:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-445344387  

~~Sorry @awulkiew , assertions are currently not possible without a major rewrite. It is because of the get_turn_info_la and get_turn_info_ll (which I did not write, neither of them). They call several strategies (e.g. collinear) of get_turn_info (for areal), which asks for points "k" all the time. And probably there are way more calls to pk, qk or their robust equivalents.  
I think we have to keep that now. They did already access these points in previous implementation. Because these points were passed already (it is just the "next" point - and it is valid - but the line ends there, so effectively it is probably the first point). But there was no assertion. I currently cannot rewrite all of these...  
But we should certainly revisit this.~~  
  
Scratch that. I tried again and it needs some additional conditions, but it is not a major rewrite. I currently have it without assertions (but a few results linear/linear are still wrong, working on that).  
It should not access point pk/qk if there is no... even though it did it until now.

---

## Comment 48

> Username: barendgehrels  
> Created_at: 2018-12-08 20:41:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-445488381  

Right, it is ready again, all tests pass (now in debug mode - to verify assertions)

---

## Review 49 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-13 01:35:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-184471162  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_for_endpoint.hpp

```diff
 418 |+                     int const side_pj_q2 = sides.apply(range2.at(1), range2.at(2), range1.at(1));
 419 |+                     int const side_pj_x = sides.apply(range2.at(0), range1.at(0), range1.at(1));
 420 |+                     int const side_qk_x = sides.apply(range2.at(0), range1.at(0), range2.at(2));
```

> Username: awulkiew  
> Created_at: 2018-12-13 01:35:26 UTC  
> Updated_at: 2018-12-13 01:43:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r241246558  

It is probably me who wrote it. I don't remember exactly why turns are checked this way, I'd have to analyse the whole file and maybe debug. However I may point you in a right direction right now. This is probably for turns where linear geometries are involved, correct? With linear geometries the turns have slightly different meaning than in areal/areal case. For instance the equality doesn't mean that the whole segment was the same but that the intersection point was equal to one of the points of the geometry. Turns hold additional data indicating whether prior to the intersection point the segments were collinear or are crossing at the intersection point, etc. AFAIR this had something to do with the fact that linear geometries are not ordered like areal and that from various geometrically equal sets of segments I needed the same set of turns, no matter the direction of the segments. E.g. for areal/areal one relative direction could result in i/i operations but with reverse segments the operations would be x/x. And this is true for all other operations. But linear turns would be the same.  
  
So AFAIR parts of this code are there to transform ordered areal turns into unordered linear turns. Sometimes additional info had to be calculated. I didn't check this for sure but this might have been such case.

> Username: awulkiew  
> Created_at: 2018-12-13 01:48:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r241248574  

Furthermore, this is the code calculating turns for endpoints, i.e. the first or the last point of a linestring. There is no such thing in areal geometries. However in this case we have to know how the other geometry behaves at the endpoint, when one geometry ends or begins. And since the geometry either begins or ends at intersection point maybe this is why "non-segment" points from other geometries are considered.


---

## Review 50 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-13 01:37:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-184471708  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_for_endpoint.hpp

```diff
 474 |                     std::pair<operation_type, operation_type>
 506 |-                         operations = operations_of_equal(side_calc);
 475 |+                         operations = operations_of_equal(side_pi_q2, side_pi_x, side_qk_x);
```

> Username: awulkiew  
> Created_at: 2018-12-13 01:37:39 UTC  
> Updated_at: 2018-12-13 01:37:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r241247045  

So unless we know what's happening exactly we should probably be careful about changing this code. But if all of the tests pass with this change it should be ok I think.


---

## Review 51 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-13 01:39:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-184472063  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_for_endpoint.hpp

```diff
 587 |         // oppositely
 623 |-         if ( side_pk_q2 == 0 && side_pk_p == side_qk_p )
 588 |+         if ( side_1 == 0 && side_2 == side_3 )
```

> Username: awulkiew  
> Created_at: 2018-12-13 01:39:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r241247360  

Out of curiosity, why did you decide to change the names of variables to less meaningful? Above I see a comment referencing specific points so this condition looks unrelated now.


---

## Review 52 [Commented]

> Username: awulkiew  
> Created_at: 2018-12-13 01:54:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/531#pullrequestreview-184474631  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_la.hpp

```diff
 740 |+                     int const side_pj_q2 = sides.apply(range_q.at(1), range_q.at(2), range_p.at(1));
 741 |+                     int const side_pj_x = sides.apply(range_q.at(0), range_p.at(0), range_p.at(1));
 742 |+                     int const side_qk_x = sides.apply(range_q.at(0), range_p.at(0), range_q.at(2));
```

> Username: awulkiew  
> Created_at: 2018-12-13 01:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#discussion_r241249584  

Is this new way of expressing points more readable to you (e.g. instead of `i()`, `j()`, `k()`)? For me I don't know, looks confusing. Granted, if someone doesn't know what `i`, `j` or  `k` means it's also confusing but at least one cane distinguish one from the other on first sight. Maybe it's a matter of getting used to it. Is it? How do you feel about it after working with it for a while?


---

## Comment 53

> Username: awulkiew  
> Created_at: 2018-12-13 02:01:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-446814678  

Looks good, I'm only worried about the replacement of `side_calculator_for_endpoint` with manually calculated sides. AFAIU the whole point of it was to have this code used several times encapsulated as a tool. Why have you decided to remove it and replace with code looking less clear?

---

## Comment 54

> Username: barendgehrels  
> Created_at: 2018-12-13 07:33:46 UTC  
> Updated_at: 2018-12-13 17:50:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-446870109  

IMO it is not less clear. The code was using named sides, but substituting them with completely other sides, even from different sources. I was really surprised by that and consider it as wrong.  
  
In this new version you can a bit better see what is happening there, which is still not 100% clear, but IMO more clear than just call encapsulated magic in the wrong way.  
  
I had to do it in really small steps to avoid breaking anything, it took me quite some time but all is equivalent as it was before, and all tests pass of course.

---

## Comment 55

> Username: barendgehrels  
> Created_at: 2018-12-13 17:56:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447061619  

@awulkiew About your question, related to the new method of using things as range_q.at(0):  
  
> Is this new way of expressing points more readable to you (e.g. instead of i(), j(), k())?  
  
I was quite used to i,j and k - but I noticed that @vissarion kind of objected to this way of working, and I think I agree with him. So having range-like methods as at(0) (I also considered just subscripting) make it more clear which point is meant, then just alphabetical order i,j,k,l (l, so L, would have been new, but would have been really confusing with 1).  
  
So to answer your question directly, yes, I think that at(0) etc is more readable to me than .i()

---

## Comment 56

> Username: barendgehrels  
> Created_at: 2018-12-13 17:59:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447062601  

About your question  
  
> Out of curiosity, why did you decide to change the names of variables to less meaningful?  
  
Because the names were wrong. It used names like "pk" - while, as you mentioned, there is indeed no "pk" in end-points. It is another point, and which one depends on the situation. It is called 5 or 6 times in different ways, I could not come up with a common name, also because I don't understand all the tiny details of this code.

---

## Comment 57

> Username: barendgehrels  
> Created_at: 2018-12-13 18:08:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447065400  

Finally, about your comment:  
  
> So AFAIR parts of this code are there to transform ordered areal turns into unordered linear turns.  
  
I did not realize they were unordered, and I don't think they are. Is that really the case? But for sure, I understand that on the end-points there is different behaviour, and different decisions should be made. What I don't understand (at least not understood) was that side calculator was used, a points were inserted for the k-points, and all looked like there were general calculations happening, but behind the screens there were sides calculated totally different from the normal cases (and called like the normal cases). That was my incentive to change it, besides, of course, the need to pass a range instead of points (which I could not do here) and to access the ranges differently, and pass them differently, as was coming from review remarks...  
  
Also, this code was called from both linear/linear and linear/areal.  
  
Are there bulk tests for these cases, inside or outside (at MySQL side) our codebase? I'm curious if it just always works...  
  
Of course I'm willing to give more meaningful names if I know them, and to understand the code better, and refactor some more things if necessary - but for now I thought this was already quite a step and, as a step itself, consistent.

---

## Comment 58

> Username: awulkiew  
> Created_at: 2018-12-13 18:56:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447080232  

We have the tests here:  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/overlay/get_turns_linear_linear.cpp  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/overlay/get_turns_linear_areal.cpp  
  
So debugging them, stepping inside and seeing in which scenarios which sides are calculated here and which points are taken into account should give us the answer. I'll check it out too. Btw, if you're using Visual Studio you could use the extension I wrote to easily see the geometries, I don't know if you know about it (https://marketplace.visualstudio.com/items?itemName=AdamWulkiewicz.GraphicalDebugging).

---

## Comment 59

> Username: awulkiew  
> Created_at: 2018-12-13 22:05:07 UTC  
> Updated_at: 2018-12-13 22:22:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447137234  

Ok, so this is the code handling a case when the intersection point is at the first or last point of the linestring (geometry P) and the second point of a segment of the other linestring or ring (geometry Q). So as usual we want to check which segment has to be followed when intersection or union is calculated. Because there is no 3rd point of the first geometry (and I probably wanted to use existing utility for this) we have to generate some point and it's sufficient to take the first point of Q.  
  
E.g. in this case: `"LINESTRING(1 0,1 1)", "LINESTRING(0 0,1 0,2 0)"` (https://github.com/boostorg/geometry/blob/develop/test/algorithms/overlay/get_turns_linear_linear.cpp#L40) these are the initial segments (P - 1 red segment, Q - 2 green segments):  
  
![image](https://user-images.githubusercontent.com/1226951/49967895-6e588280-ff24-11e8-9b3a-266ca11d61e3.png)  
  
and these are the **new** 2 blue segments for P (let's call it **P'**) with the added first point from Q and 2 segments of Q (green):  
  
![image](https://user-images.githubusercontent.com/1226951/49968039-e2932600-ff24-11e8-900b-fec08fc385dd.png)  
  
So now we can check the sides "the regular way" using the existing tools, i.e. `side_calculator` to check sides of **P'** and Q and `IntersectionInfo` to check spikes of Q.  
  
With that said in L/L case (2 linear geometries) 2 sides checks would be enough: Pi or Pj WRT Q1 and Q2 (so `side_pk_q2` and `side_pk_p` (**P'**) because this is the same as `side_pk_q` in the original code) followed by spike check to see if the side is opposite. There are additional conditions in `operations_of_equal` because this function is used also explicitly in the L/A (linear and areal) case (e.g. here https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_turn_info_la.hpp#L780), so if the segment of linear P (or second segment of **P'**) is on the left of Q, it's outside Q which means union on P, and if it's right of Q this means intersection on P. Because the areal geometries are ordered clockwise. And by left of Q I mean **P'** k (which is Pj for first and Pi for last segment of a linestring) turning more left than Qk WRT Q1 (in `operations_of_equal` named `_p`, so of **P'**). So yes:  
- the naming is messed up,  
- for L/L this additional side calculation could be removed which implies different implementation for L/A.

---

## Comment 60

> Username: awulkiew  
> Created_at: 2018-12-13 22:31:49 UTC  
> Updated_at: 2018-12-13 22:34:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447144528  

I edited the comment to make it more clear what I'm talking about ;)  
  
I don't suggest that this code should be refactored/cleaned as a part of this PR. I just wanted to help understanding it.  
  
Anyway, I agree with merging it together with possible change of names of variables.  
  
So this would probably be the key:  
* `first1`  
  * `side_pk_q2` -> `side_pj_q2`  
  * `side_pk_p` -> `side_pj_q`  
  * `side_qk_p` -> `side_qk_q`  
* `last1`  
  * `side_pk_q2` -> `side_pi_q2`  
  * `side_pk_p` -> `side_pi_q`  
  * `side_qk_p` -> `side_qk_q`

---

## Comment 61

> Username: awulkiew  
> Created_at: 2018-12-13 22:52:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447150428  

One more clarification, by "unordered" I meant that linear geometries are not CW nor CCW and that there is no interior or exterior on left or right side of linear geometry. That regardless of the relative direction of this geometry the methods and operations are the same. They allow to simply follow the linear geometry and generate intersection or union or stop at `x`. Previously when the regular/areal turns were used for linear geometries different turns were generated based on the side of the geometries, like i/i vs x/x etc. Maybe "unordered" was not a good name, my point was that the operations and methods may mean something different than you're used to. E.g. operation `x` means "the end of linestring" and method `e` may be generated for the first endpoint of linestring because it means "intersection point equal to points of both geometries", not "the segments before arriving at IP are equal" as in A/A case, etc.

---

## Comment 62

> Username: barendgehrels  
> Created_at: 2018-12-13 23:08:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447154263  

Thanks a lot for your clarifications! I'll continue later.

---

## Comment 63

> Username: barendgehrels  
> Created_at: 2018-12-14 19:45:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447434045  

Thanks again for your comments and clear pictures.  
  
Sure, I know your debugging tools, certainly. But I'm always using QtCreator now - and not Visual Studio. I remember that you did have a tool for that too, but (at that time) it did slow things down, sometimes giving issues, and besides that I usually have the need to add more and more debug information, so therefore I rely on SVG creation and do not visualize geometries themselves in the IDE.  
  
It now becomes clear to me why a point of Q was substituted for P. But I still think it was mainly a trick to be able to use the side calculator. That need is gone now, because (by applying ranges) it became clear that it was messing names up (I don't mean implementation, but just the names, as you confirmed).  
If that substitution is not necessary anymore, it is trivial to calculate just the point of p (pj in this case) w.r.t. q1 or q2.  
  
For which you provide the key, as far as I can see, and I could use those names (in the calling functions, so i/o x. But note that they are called as, for example the last one:  
`int const side_qk_x = sides.apply(range2.at(0), range1.at(1), range2.at(2));`  
If this is side qk_q - why is the second point then coming from range 1? I tried to make it consistent with your naming (so use range2.at(1)) but... that gives me errors. So it is not side_qk_q but the side of qk w.r.t. a segment made from qi to pj.   
  
Or maybe I did not understand you completely...

---

## Comment 64

> Username: awulkiew  
> Created_at: 2018-12-14 21:02:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447478483  

I'm playing with the code in develop and replacing the second parameter of `side_calc` at line 432 (`first1`) from `ri1` to `rj2` and at line 485 (`last1`) from `rj1` to `rj2` doesn't change the results for me. At least all of the set operations tests pass. So in case of the first segment (`first1`) the **P'** is changed from `ri2, ri1, rj1` to `ri2, rj2, rj1`, or in the new notation from `rng2.at(0), rng1.at(0), rng1.at(1)` to `rng2.at(0), rng2.at(1), rng1.at(1)`  
  
Anyway, I also tried to remove `side_calculator` at all and at line 428 put this:  
  
    auto side = inters.get_side_strategy();  
  
    int const side_pj_q2 = side.apply(rj2, rk2, rj1);  
    int const side_pj_q1 = side.apply(ri2, rj2, rj1);  
    int const side_qk_q1 = side.apply(ri2, rj2, rk2);  
  
    std::pair<operation_type, operation_type>  
        operations = operations_of_equal(side_pj_q2, side_pj_q1, side_qk_q1);  
  
and at line 482:  
  
    auto side = inters.get_side_strategy();  
  
    int const side_pi_q2 = side.apply(rj2, rk2, ri1);  
    int const side_pi_q1 = side.apply(ri2, rj2, ri1);  
    int const side_qk_q1 = side.apply(ri2, rj2, rk2);  
  
    std::pair<operation_type, operation_type>  
        operations = operations_of_equal(side_pi_q2, side_pi_q1, side_qk_q1);  
  
of course in `operations_of_equal` take `(int side_px_q2, int side_px_q1, int side_qk_q1)`, where `px` means that it's either `pi` or `pj` so the endpoint of the segment P which is not the intersection point. After this change `get_turns_linear_linear` test passes as well.

---

## Comment 65

> Username: barendgehrels  
> Created_at: 2018-12-14 21:23:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447483269  

Right, I think I tried the same thing (but with errors...)  
I propose to merge it as it is now, and I don't object, of course, if you change the variable names and/or the substituted points afterwards.

---

## Comment 66

> Username: awulkiew  
> Created_at: 2018-12-14 21:34:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447485952  

Overlay tests also pass for me. In `get_turns_la` I adapted `operations_of_equal` to new arguments, e.g. like that:  
  
    std::pair<operation_type, operation_type>  
        operations = get_info_e::operations_of_equal(side_calc.pk_wrt_q2(), side_calc.pk_wrt_p1(), side_calc.qk_wrt_p1());  
  
I also changed the names of points passed to `side_calculator` when it was possible. Note that at line 793 the intersection point is in the middle of Q1 and is equal to `pi` which means that a new segment `qi`->`pi` has to be considered so in this case sides are calculated WRT this new segment and not Q1. And later the same for last, the intersection point may be in the middle. But this is because `side_calculator` is used, if it were not used and sides calculated manually we could probably instead simply use Q1 because it has the same direction as the segment from `qi` to the intersection point at the Q1.

---

## Comment 67

> Username: awulkiew  
> Created_at: 2018-12-14 21:39:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447487122  

Do I understand correctly that you do not get the errors with the code as it is now, correct? Only after making changes? And you suggest I play with it after the merging? If that's the case I agree.

---

## Comment 68

> Username: barendgehrels  
> Created_at: 2018-12-14 23:09:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447505899  

Sure, that is what I suggested. So I did not get the errors as it is now.  
Thanks for merging! And thanks again for reviewing

---

## Comment 69

> Username: awulkiew  
> Created_at: 2018-12-15 01:01:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447522183  

Follow-up: https://github.com/boostorg/geometry/pull/540  
It worked for me out of the box. Maybe you had problems because of some typo?  
Anyway, let's move the discussion there.

---

## Comment 70

> Username: barendgehrels  
> Created_at: 2018-12-15 09:33:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/531#issuecomment-447553843  

All right - I was tired, probably not a typo but tried one of them, not the pair together. So obviously that didn't work... Thanks, will go to that new PR

---
