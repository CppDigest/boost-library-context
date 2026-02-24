# #609 Add infinite line (currently in 2D, implemented with the "general form") [Merged]

> Username: barendgehrels  
> Created at: 2019-07-03 13:35:46 UTC  
> Updated at: 2019-07-18 11:54:29 UTC  
> Merged at: 2019-07-10 14:55:13 UTC  
> Closed at: 2019-07-10 14:55:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/609  

This pull request adds an **infinite line** in general form. It is not (yet) a concept, but its code was already used in two places, and it will also be used in the rescaling replacement.  
  
A third place (direction_code_ used some other implementation but that is now replaced by this general form too. This fixes a small bug where lines were exactly vertical.  
  
In a later phase we might change it and make it concept based, such that we have the intersection between two infinite lines.  
  
It is Cartesian. In Spherical or Geographical, it might maybe be represented by a Great Circle or Ellipse.  
  
Including unit test.  
  
It also removes some debug code only for unit tests in direction_code (removal was already marked with a TODO)

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2019-07-04 09:50:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-257944922  

Thank you.   
  
My main comment is that I do not see the reason for the line to be in `arithmetic` namespace. Moreover, since the whole construction and computations are for cartesian only why not creating a cartesian strategy from it?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-07-04 12:24:43 UTC  
> Updated_at: 2019-07-04 12:33:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#issuecomment-508463880  

My understanding is that this is a utility which will be used only in case of cartesian CS, e.g. in strategies. Should it be a strategy? Are there equivalent concepts in other coordinate systems we'd like to use?  
  
Btw, another place where it could be put is util/math. But I think arithmetic is acceptable. We already have cartesian-vector-related stuff there.

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 12:25:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258013085  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 183 |+         typedef arithmetic::general_form<coordinate_type> gf;
 184 |+         gf const p = arithmetic::construct_line<coordinate_type>(p0, p1);
 185 |+         gf const q = arithmetic::construct_line<coordinate_type>(p1, p2);
```

> Username: awulkiew  
> Created_at: 2019-07-04 12:25:33 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300375559  

Why not simply use a constructor?

> Username: barendgehrels  
> Created_at: 2019-07-10 08:38:52 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r301947229  

See my other answer, it is concept based. I can rename it to `make` which is more common.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 12:29:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258014819  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 181 |+             output_point_type const& p2)
 182 |+     {
 183 |+         typedef arithmetic::general_form<coordinate_type> gf;
```

> Username: awulkiew  
> Created_at: 2019-07-04 12:29:01 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300376949  

I suggest to change the name because in general context it's not clear general form of what is this. I'd rather explicitly name it `line_2d`, `general_2d_line_form` or something similar. Or even more explicitly `cartesian_line_2d`. Because this is only 2d and only cartesian. It's not really that much "general". ;)

> Username: barendgehrels  
> Created_at: 2019-07-04 21:46:11 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300503052  

Hi @awulkiew @vissarion thanks for all your comments.  
It is called general because it implements the so-called "general form" (otherwise it would be "generic").  
For the rest I come back to all your remarks, hopefully next Sunday

> Username: awulkiew  
> Created_at: 2019-07-05 03:06:24 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300528828  

@barendgehrels my point is that it is a "general form" of something specific, i.e. equation of 2d cartesian line. I don't know if "general form" is only used WRT line equations but when I was searching the web I found that this name was always put in quotes so it seems to be rather colloquial term. And I'd assume that many things can have "general form" whatever that may be.

> Username: barendgehrels  
> Created_at: 2019-07-10 08:25:54 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r301941983  

Right, sorry for the slight delay. I'm OK with a name change, and with a move.  
  
Note that this class is more useful than just a utility. It implements an infinite line, like we have a point, segment, linestring, and this is a (non OGC) infinite line. Currently in 2d, yes. Over the years there have been questions about this (calculate intersection between two infinite lines) and this code already implements that.  
  
So in that sense it could be moved to the geometry/geometries folder.  
  
But I did not make it yet as generic as all the other stuff, so it is not yet concept based.  
  
Anyway, this is the reason I kept the class very concise and did not put a normal constructor there.  
  
More answers to come. I will also update the description.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 12:36:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258018499  

📁 include/boost/geometry/arithmetic/general_form.hpp

```diff
  31 |+         : a(GeneralType())
  32 |+         , b(GeneralType())
  33 |+         , c(GeneralType())
```

> Username: awulkiew  
> Created_at: 2019-07-04 12:36:54 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300379583  

Why not `a(0)`? :) What is our decision about zero-initialization regarding new code? Because we agreed to leave the old code as it is but what about newly added one?

> Username: barendgehrels  
> Created_at: 2019-07-10 08:26:19 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r301942152  

Will update. I wrote this already before our recent discussion here.


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 12:37:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258019002  

📁 include/boost/geometry/arithmetic/general_form.hpp

```diff
  41 |+     GeneralType b;
  42 |+     GeneralType c;
  43 |+     bool normalized;
```

> Username: awulkiew  
> Created_at: 2019-07-04 12:37:59 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300379953  

Do we only need 2d version?

> Username: barendgehrels  
> Created_at: 2019-07-10 08:29:48 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r301943511  

For now yes. The form which I called "general form" in 3D is a plane. Did not dive into that. But of course infinite lines in 3D do exist.


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 12:38:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258019424  

📁 include/boost/geometry/arithmetic/general_form.hpp

```diff
  54 |+     result.c = -result.a * x1 - result.b * y1;
  55 |+     return result;
  56 |+ }
```

> Username: awulkiew  
> Created_at: 2019-07-04 12:38:56 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300380271  

If we had n-dimensional version this would probably have to be reimplemented.

> Username: barendgehrels  
> Created_at: 2019-07-10 08:30:06 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r301943645  

I don't think that is a problem.


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 12:41:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258020820  

📁 include/boost/geometry/arithmetic/general_form.hpp

```diff
  47 |+ inline
  48 |+ general_form<GeneralType> construct_line(Coordinate const& x1,
  49 |+     Coordinate const& y1, Coordinate const& x2, Coordinate const& y2)
```

> Username: awulkiew  
> Created_at: 2019-07-04 12:41:49 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300381345  

Is this the only kind of line which could be constructed from 4 numbers? This could either be the constructor of `general_line` type (or whatever other name it'd have) or this function could take the `general_line` as an argument in case you have more line types that could be constructed in the future.  
  
This would also solve the n-dimensional case because this ctor/function should only be implemented for 2d.

> Username: barendgehrels  
> Created_at: 2019-07-10 08:30:30 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r301943796  

Most code we have is for 2d


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 12:51:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258025542  

📁 include/boost/geometry/arithmetic/general_form.hpp

```diff
  80 |+ template <typename Point, typename GeneralType>
  81 |+ inline
  82 |+ bool get_intersection(Point& ip,
```

> Username: awulkiew  
> Created_at: 2019-07-04 12:51:54 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300385032  

Typically then something is called `get_XXX` then it returns `XXX`. It is also a mixture of `intersects` and intersection (thought in this case it should take the point as the last output argument). But at the same time it has different arguments than these functions. I think I'd probably call it `intersection()` but put the `Point` as the last argument as it is the case with `bg::intersection()`.

> Username: barendgehrels  
> Created_at: 2019-07-10 08:31:40 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r301944292  

I'm OK with changing the name and putting the point as last, sorry about that.  
  
BTW: if the infinite line will indeed be one of our concepts, the general intersection function should be able to find its intersection


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 12:53:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258026502  

📁 include/boost/geometry/arithmetic/general_form.hpp

```diff
 108 |+ typename select_most_precise<GeneralType, CoordinateType>::type
 109 |+ side_value(general_form<GeneralType> const& f,
 110 |+     CoordinateType const& x, CoordinateType const& y)
```

> Username: awulkiew  
> Created_at: 2019-07-04 12:53:54 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300385758  

Right, so this is another 2d-only function, but here it's ok since it takes `general_form` so it would be possible to implement n-d version by overloading it.


---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 12:58:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258028968  

📁 include/boost/geometry/arithmetic/general_form.hpp

```diff
 117 |+     // Also, this gives positive values for points left to the line,
 118 |+     // and negative values for points right to the line.
 119 |+     return f.a * x + f.b * y + f.c;
```

> Username: awulkiew  
> Created_at: 2019-07-04 12:58:55 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300387591  

I wonder how good numerically this new solution is. Does it really behave better in various cases than what we have now or does it simply work for some specific cases we test right now but can theoretically make it worse in other cases?

> Username: barendgehrels  
> Created_at: 2019-07-10 08:33:20 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r301944996  

It behaves numerically better than the Kramer's rule (that we currently had). Therefore I replaced it in buffer and with that fixed a bug. I will come back later with more info. I'm testing it quite intensively.

> Username: barendgehrels  
> Created_at: 2019-07-10 11:22:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r302013846  

The calculations between Cramer's rule are actually similar to this code. So it should be numerical equivalent. It is more the way how things are expressed, which is now simpler (and that was actually fixing that bug). Besides that, in a future, this line parameters might be stored in a prepared geometry, improving performance


---

## Review 12 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-04 13:04:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-258031711  

📁 include/boost/geometry/arithmetic/general_form.hpp

```diff
 139 |+ template <typename GeneralType>
 140 |+ inline
 141 |+ bool similar_direction(const general_form<GeneralType>& p,
```

> Username: awulkiew  
> Created_at: 2019-07-04 13:04:20 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r300389632  

So the question is, what does "similar" mean? "Maybe up to 45 degrees"? I think the name doesn't express this well enough. Besides does such non-well-defined function is needed? If in some algorithm a check like: "angle between lines < 90 deg or < 45 deg" then this function should be named specifically expressing the desired condition and the condition in the function should check exactly what it is supposed to check.

> Username: barendgehrels  
> Created_at: 2019-07-10 08:37:45 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r301946723  

The documentation states:   
```  
Returns true for two lines which are supposed to be (close to) collinear  
(which is not checked) and have a similar direction  
```  
We need a function which returns true of collinear lines that are opposite, and false if they have the same direction. This simple function does the trick. But what is collinear - that is based on numerical precision. The result should be reliable with all the epsilons we use. So this function does not do any check for collinearity, and does not use any epsilon. Its implementation is straightforward, and (basically as a side effect) it returns true for lines even 30 degrees apart. Which indeed have a `similar` direction (therefore I still like the name, it should not be too precise)

> Username: awulkiew  
> Created_at: 2019-07-10 11:09:12 UTC  
> Updated_at: 2019-07-10 11:13:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r302008938  

> We need a function which returns true of collinear lines that are opposite, and false if they have the same direction.   
  
So the precondition is that the arguments are two collinear lines (so this is checked before somewhere) and then they either may be going into the same direction or the opposite and you have to check that, correct? If that's correct then this function has strictly defined purpose. Not to check similarity but to check the direction of two collinear lines. So why not name it to something like: `same_collinear_direction` or since in the code we call this being opposite why not simply: `are_opposite` or `check_opposite`?

> Username: barendgehrels  
> Created_at: 2019-07-10 11:24:14 UTC  
> Updated_at: 2019-07-10 11:24:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r302014376  

I don't know why `same_collinear_direction` is more clear than `similar_direction`. With `are_opposite` I have the objection that it does not check collinearity itself.   
But I'm not opposed to renaming ;-)

> Username: awulkiew  
> Created_at: 2019-07-10 13:30:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r302065263  

I agree, the names I proposed are also not perfect. If you think of anything better then rename it. If not I'm ok with `similar_direction`.

> Username: barendgehrels  
> Created_at: 2019-07-10 14:55:05 UTC  
> Updated_at: 2019-07-10 14:55:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#discussion_r302110812  

Thank you, for now I will merge it - maybe we have more ideas later.


---

## Comment 13

> Username: vissarion  
> Created_at: 2019-07-05 10:12:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#issuecomment-508708903  

>My understanding is that this is a utility which will be used only in case of cartesian CS, e.g. in strategies. Should it be a strategy? Are there equivalent concepts in other coordinate systems we'd like to use?  
  
I guess this reduces to whether we need rescaling for other coordinate systems, since as far as I understand this utility is for rescaling.

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2019-07-10 11:26:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#issuecomment-510021826  

I reworked the code, it is now splitted into an `infinite_line` (class and file), `make` ( to construct it) and `infinite_line_functions` (I did not want to split all these in 4 mini files). I think it is better than it was, thanks for your comments. Please let me know your new opinions.

---

## Review 15 [Approved]

> Username: vissarion  
> Created_at: 2019-07-10 11:41:02 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/609#pullrequestreview-260035992  

Thanks for the changes. I am ok with merging.

---

## Comment 16

> Username: awulkiew  
> Created_at: 2019-07-10 13:30:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/609#issuecomment-510061792  

Thanks. I'm ok as well.

---
