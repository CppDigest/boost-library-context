# #479 Fix for empty spherical union [Merged]

> Username: awulkiew  
> Created at: 2018-05-03 23:42:55 UTC  
> Updated at: 2019-11-06 12:52:57 UTC  
> Merged at: 2019-11-06 12:52:56 UTC  
> Closed at: 2019-11-06 12:52:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/479  

This PR contains fixes for some of the issues raised here: https://github.com/boostorg/geometry/issues/475.

---

## Comment 1

> Username: vissarion  
> Created_at: 2018-05-07 07:52:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#issuecomment-386987779  

thanks, I am ok with it.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2018-05-10 16:17:11 UTC  
> Updated_at: 2018-05-10 16:17:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#issuecomment-388104062  

This PR fixes more or less half of the issues raised. In my esstimation handling the rest requires more severe changes in overlay. I'd rather do this in a separate PR.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2019-10-30 01:25:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#issuecomment-547698952  

@barendgehrels What do you think? There are some changes made in this PR which are similar to the ones you made recently, e.g. greater enlargement of sections in `secionalize()`.

---

## Review 4 [Changes requested]

> Username: barendgehrels  
> Created_at: 2019-10-30 09:11:19 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/479#pullrequestreview-309043282  

I would like to try it without the strategy, as indicated there in the comments.  
For the rest (w.r.t the calculations in spherical), I'm fine.

📁 include/boost/geometry/policies/robustness/segment_ratio.hpp

```diff
  56 |-         return lhs.numerator() * rhs.denominator()
  57 |-              < rhs.numerator() * lhs.denominator();
  56 |+         Type const l = lhs.numerator() / lhs.denominator();
```

> Username: barendgehrels  
> Created_at: 2019-10-30 09:00:32 UTC  
> Updated_at: 2019-10-30 14:39:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#discussion_r340492635  

Please avoid using `l` as a variable name

> Username: awulkiew  
> Created_at: 2019-10-30 12:37:12 UTC  
> Updated_at: 2019-10-30 14:39:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#discussion_r340587685  

Is `a` and `b` clear enough?

> Username: barendgehrels  
> Created_at: 2019-10-30 13:48:40 UTC  
> Updated_at: 2019-10-30 14:39:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#discussion_r340623312  

Sure, fine for me.


📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 781 |- template <typename Sections>
 782 |- inline void enlarge_sections(Sections& sections)
 781 |+ // TODO: If it depends on CS it should probably be made into strategy.
```

> Username: barendgehrels  
> Created_at: 2019-10-30 09:10:31 UTC  
> Updated_at: 2019-10-30 14:39:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#discussion_r340497239  

I don't think it should be a strategy.  
  
As far as I can see, just increasing the sections by a larger amount than just an epsilon is OK for all coordinate systems. Because otherwise some intersections can be missed in rare cases.  
Enlarging the sections does no harm, other than that it might compare a few segment pairs more.  
  
Can't you just expand by a larger value, regardless of rescaling or coordinate system? Did you try that? To me that sounds better than introducing this strategy.

> Username: awulkiew  
> Created_at: 2019-10-30 12:29:20 UTC  
> Updated_at: 2019-10-30 14:39:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#discussion_r340584459  

Yes right now, enlarging them by greater value should work. Providing various strategies allows to tweak enlargement values for specific formulas. It is not needed to pay the performance price in cartesian because andoyer formula has low accuracy in geographic.  
  
However, I'm considering a possibility of passing epsilon used e.g. in point comparisons or distance comparisons in a strategy. It's because various formulas have different accuracy. If this was possible then a user would be able to define the epsilon too to something even greater and then in order to be consistent it should be possible to change the enlargement value as well.

> Username: barendgehrels  
> Created_at: 2019-10-30 13:49:10 UTC  
> Updated_at: 2019-10-30 14:39:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#discussion_r340623612  

OK, that sounds interesting and more generic.


---

## Comment 5

> Username: awulkiew  
> Created_at: 2019-11-05 13:04:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#issuecomment-549814063  

@barendgehrels I addressed the changes you requested. Are you ok with merging?

---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2019-11-06 09:34:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/479#pullrequestreview-309313340  

>   
>   
> @barendgehrels I addressed the changes you requested. Are you ok with merging?  
  
Eehm, actually I thought that you considered a different solution too. You mentioned:   
_However, I'm considering a possibility of passing epsilon used e.g. in point comparisons or distance comparisons in a strategy._  
  
What is the state of that?  
  
Anyway, as said, I'm not so enthousiastic about this epsilon-box-enlarging strategy, per coordinate-system different. It is still there. Did you try enlarging the box by just a value? And what is the largest value you need?  
  
If that is satisfactory (also for Cartesian, and I think I can judge that and measure performance), then that has my preference. If we don't get that right, then I'm OK with this solution of course.

📁 include/boost/geometry/policies/robustness/segment_ratio.hpp

```diff
  57 |+         Type const b = rhs.numerator() / rhs.denominator();
  58 |+         return ! geometry::math::equals(a, b)
  59 |+             && a < b;
```

> Username: barendgehrels  
> Created_at: 2019-10-30 15:46:48 UTC  
> Updated_at: 2019-11-06 09:34:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#discussion_r340699770  

thanks


---

## Comment 7

> Username: awulkiew  
> Created_at: 2019-11-06 12:39:59 UTC  
> Updated_at: 2019-11-06 12:49:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#issuecomment-550291786  

> What is the state of that?  
  
It's an idea for the future. We'd need umbrella strategies framework to allow the users to pass epsilon in a reasonable way. I.e. it should be done in a standalone strategy. Otherwise (right now) we'd have to take it in all strategies of operations where e.g. internally points are checked for equality, so most of them.  
  
> I'm not so enthousiastic about this epsilon-box-enlarging strategy, per coordinate-system different. It is still there. Did you try enlarging the box by just a value? And what is the largest value you need?  
  
I think enlarging by min/max scaled by epsilon is correct, or at least more correct than picking a constant value. It's because other operations are done like that, e.g. point equality is checked by scaling the coordinates by epsilon. If you wanted to pick a single largest value it would still depend on coordinate system. In cartesian the coordiantes can be arbitrary so in the worst case, at the end of the `double` range, e.g. when points are compared for equality this comparison is done WRT machine epsilon like that: `numeric_limits<double>::epsilon() * numeric_limits<double>::max()` (this for coordinates at the upper bound of `double` range) [1] so around: `2.2e-16 * 1.8e+308` ~ `4.0e+292`. In order to represent points correctly (at the edges of bounding box) bounding box should be enlarged by a number from `2.2e-16` to `4.0e+292` depending on the coordinates.  
  
And this is only when we consider point comparisons. In the library we have many different operations having different accuracies. We also compare distances which errors depend on coordinates, the distance itself and formula used. And many more.  
  
My guess is that constant number will fail with sufficiently large geometries. E.g. considering only point comparisons I think that because for buffer the bounding box is enlarged by `0.001` this algorithm should start giving wrong results when coordinates are around `4503599627370`.  
  
[1] See:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/math.hpp#L186  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/math.hpp#L118

---

## Review 8 [Approved]

> Username: barendgehrels  
> Created_at: 2019-11-06 12:47:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/479#pullrequestreview-312403637  

Hi @awulkiew , thanks for your explanation.  
Right, I'm OK with merging this.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2019-11-06 12:50:43 UTC  
> Updated_at: 2019-11-06 12:51:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#issuecomment-550295359  

Btw, it could actually be an interesting experiment for all algorithms, not only buffer, to scale up the input several times and see at which point the output is different or the algorithm fails.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2019-11-06 12:52:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/479#issuecomment-550296009  

@barendgehrels thanks for the review.

---
