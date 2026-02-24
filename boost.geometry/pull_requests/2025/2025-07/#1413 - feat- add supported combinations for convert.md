# #1413 feat: add supported combinations for convert [Merged]

> Username: barendgehrels  
> Created at: 2025-07-12 11:41:38 UTC  
> Updated at: 2025-07-17 16:47:05 UTC  
> Merged at: 2025-07-17 16:47:00 UTC  
> Closed at: 2025-07-17 16:47:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1413  

This PR adds combinations for `convert`. I need it for a next step (an alternative for `dissolve`).  
  
It adds support for:  
* areal features to multi-linestring  
* linear and areal features to multi-point  
* and a few combinations more  
  
It also extends the test coverage to these combinations (and some combinations earlier missing).  
  
The support matrix `.qbk` is updated. I use another mechanism for that now, which:  
* is built inside the test code  
* also verifies if the test coverage is complete  
  
_The old mechanisms (there were two) both don't work currently. Also there is no cmake support. And the new mechanism is simpler, faster, and more powerful._  
  
But that will be a next PR to keep things smaller and not mixing things.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-12 11:43:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1413#pullrequestreview-3012985363  

📁 test/algorithms/Jamfile

```diff
  26 |     [ run comparable_distance.cpp      : : : : algorithms_comparable_distance ]
  27 |     [ run convert.cpp                  : : : : algorithms_convert ]
  28 |-     [ run convert_multi.cpp            : : : : algorithms_convert_multi ]
```

> Username: barendgehrels  
> Created_at: 2025-07-12 11:43:16 UTC  
> Updated_at: 2025-07-12 11:43:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1413#discussion_r2202547562  

Because it's not multi but also single to multi - it was more convenient to combine it into one source.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-12 11:45:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1413#pullrequestreview-3012985830  

📁 include/boost/geometry/algorithms/convert.hpp

```diff
 281 |-     typename Tag2 = tag_cast_t<tag_t<Geometry2>, multi_tag>,
 340 |+     typename Tag1 = tag_t<Geometry1>,
 341 |+     typename Tag2 = tag_t<Geometry2>,
```

> Username: barendgehrels  
> Created_at: 2025-07-12 11:45:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1413#discussion_r2202547952  

This was necessary to distinguish different multi to multi combinations


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2025-07-12 12:27:48 UTC  
> Updated_at: 2025-07-12 12:28:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1413#issuecomment-3065200152  

Windows Server 2019 is retired and therefore we get:  
```  
Windows Server 2019 has been retired.   
The Windows Server 2019 image has been removed as of 2025-06-30.  
For more details, see https://github.com/actions/runner-images/issues/12045  
```

---

## Comment 4

> Username: tinko92  
> Created_at: 2025-07-15 07:05:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1413#issuecomment-3072306842  

Does this change the meaning of the algorithm and should the new meaning be explained? Looking at the current develop support matrix, it so far only seems to allow conversions where the geometries can represent the same set, but e.g. linear and areal to multi point loses the interior or the interior and boundary respectively.  
  
That's not necessarily an objection to the PR, as far as I can see, convert is not based on OGC simple features, or anything that defines its intended meaning.  
  
Does this make the functionality overlap with the arguably more general, undocumented (but not in detail namespace and intended to be documented according to https://github.com/boostorg/geometry/blob/d2e5137f4070d8bbcd8ab150ad4fe42713c8ceb3/include/boost/geometry/iterators/point_iterator.hpp#L286 ) point_iterator? The point_iterator would allow me to traverse something like a multi_point without actually copying it but also use the iterator to construct a multi_point, if I do want a copy.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2025-07-15 16:15:32 UTC  
> Updated_at: 2025-07-15 16:19:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1413#issuecomment-3074305008  

Thanks for your comment!  
  
> Does this change the meaning of the algorithm and should the new meaning be explained?   
  
Maybe the documentation can be enhanced - but as far as I can tell, its meaning did not change.  
It converts from one geometry to another... It's written there.  
  
> Looking at the current develop support matrix, it so far only seems to allow conversions where the geometries can represent the same set  
  
There were not so many examples but there was already box to point supported. So you will get four points of a box.  
  
  
> Does this make the functionality overlap with the arguably more general, undocumented (but not in detail namespace and intended to be documented according to  
  
Thanks for the pointer (pun intended), I forgot that functionality. I think it should not have been outside detail - apparently I was not alert enough in the review of that file....  
It would not allow me to convert to multi-linestring...  
  
There is already https://www.boost.org/doc/libs/latest/libs/geometry/doc/html/geometry/reference/algorithms/for_each/for_each_point.html which I thought about of using underneath. But the purpose of `for_each_point` is different - for what I want to achieve now. Also here - it would not allow me to convert to multi-linestring...  
  
I need a multi-linestring representation of all (rings, polygons, multi-polygons). We had nothing similar to that (`for_each_segment` is similar but inconvenient and I need to collect it into a `multi-linestring` anyway).  
  
After considering the options, `convert` is the perfect candidate. Because it does already that, and similar things - but is incomplete. And indeed `areal` -> `linear` was not really there yet, or `areal` -> `point like` just partly. However it covers the name and the documentation page.  
  
I don't really need multi-point representations of them, at this moment, but it made the change more complete AND it was useful for me to consider the design options. Because currently it was targeted to `single` -> `multi`  
  
Hope this clarifies the intentions...

---

## Review 6 [Approved]

> Username: vissarion  
> Created_at: 2025-07-16 15:31:23 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1413#pullrequestreview-3025472029  

Thanks for the explanations for the intention of this PR. The code LGTM thanks! (only a minor comment)

📁 include/boost/geometry/algorithms/convert.hpp

```diff
 157 |         geometry::clear(destination);
 158 | 
 159 |+         constexpr bool Reverse = geometry::point_order<Range1>::value != geometry::point_order<Range2>::value;
```

> Username: vissarion  
> Created_at: 2025-07-16 15:04:33 UTC  
> Updated_at: 2025-07-16 15:31:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1413#discussion_r2210694771  

maybe lowercase "reverse" since this is not a template now

> Username: barendgehrels  
> Created_at: 2025-07-17 16:29:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1413#discussion_r2213803470  

✅


---

## Review 7 [Approved]

> Username: tinko92  
> Created_at: 2025-07-17 06:19:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1413#pullrequestreview-3028059321  

The changes look good to me and I see no new warnings or failures in the test-suite.

---
