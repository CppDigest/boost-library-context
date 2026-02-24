# #756 Non cartesian and robust convex hulls [Merged]

> Username: vissarion  
> Created at: 2020-09-25 14:56:27 UTC  
> Updated at: 2020-12-21 10:33:29 UTC  
> Merged at: 2020-12-21 10:33:22 UTC  
> Closed at: 2020-12-21 10:33:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/756  

This PR adds support for non-cartecian coordinate systems to convex hull algorithm, namely spherical and geographic. For the cartesian case it adds and tests a robust strategy implemented in https://github.com/boostorg/geometry/pull/617  
  
### Details  
  
* the graham-andrew algorithm implementation (a.k.a. Andrew's monotone chain convex hull algorithm) is moved to `algorithms` directory from agnostic strategies,  
* new umbrella strategies are added, that basically call the side strategy for each coordinate system,  
* robust strategies for cartesian CS are supported.   
  
### Robustness  
  
The file `convex_hull_robust.cpp` in `test/convex_hull` directory demonstrates what can go wrong with non robust strategies in convex hull computation (this could be extended to other geometric algorithms as well). In the examples, the resulting convex hull could (a) miss a point, (b) be slightly non-convex, (c) could result in a polygon with wrong area sign or (d) has an arbitrarily large error in the resulting area. More information about robustness here: https://people.mpi-inf.mpg.de/~mehlhorn/ftp/classroomExamplesNonrobustness.pdf   
Also as noted in https://github.com/boostorg/geometry/issues/699 the robust side predicate seems to be faster than the non-robust one (`side_by_triangle`) and could be useful to have a fast but non-robust side predicate.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-07 13:02:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-503852905  

📁 include/boost/geometry/strategies/convex_hull/cartesian.hpp

```diff
  27 |+ public:
  28 |+     template <typename Geometry>
  29 |+     static auto side(Geometry const&)
```

> Username: awulkiew  
> Created_at: 2020-10-07 13:02:10 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r500993027  

Side strategy may be called for points of the same geometry (e.g. in `is_simple`) or for points of 2 geometries (e.g. in `intersection`) and also in addition to that for some temporary point not existing in any of the geometries. In a place where this is done (e.g. in `get_turns`) the information about the original geometries may be unavailable, only arbitrary 3 points. So it is not clear which `Geometry` and how many of them would have to be passed here.  
  
So I propose to remove the parameter at all and have single side strategy only defined by the strategy.  
  
Does it make sense?

> Username: vissarion  
> Created_at: 2020-10-08 14:49:11 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r501782753  

Yes, I agree, removed.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-07 13:07:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-503857755  

📁 include/boost/geometry/algorithms/detail/convex_hull/interface.hpp

```diff
 197 |+                    geometry::point_order<Geometry>::value,
 198 |+                    geometry::closure<Geometry>::value
 199 |+                >::apply(geometry, out, strategy);
```

> Username: awulkiew  
> Created_at: 2020-10-07 13:07:37 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r500996609  

This code takes the new umbrella strategy. There is no backward compatibility.  
  
This is how it is done e.g. for `area` with `services::strategy_converter` "converting" from the old strategy to the new umbrella strategy:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/area.hpp#L240-L252  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/area/services.hpp#L42-L50  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/area/cartesian.hpp#L48-L54  
  
Unless this PR breaks the backward compatibility anyway.

> Username: vissarion  
> Created_at: 2020-10-08 14:50:57 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r501784306  

This is a breaking change, the convex hull algorithm use to take an "agnostic" convex_hull strategy "graham_andrew" but now takes a new umbrella strategy to provides a side predicate.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-07 13:09:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-503859143  

📁 include/boost/geometry/algorithms/detail/convex_hull/interface.hpp

```diff
 192 |+                                        Strategy const& strategy)
 193 |+     {
 194 |+         //BOOST_CONCEPT_ASSERT( (geometry::concepts::ConvexHullStrategy<Strategy>) );
```

> Username: awulkiew  
> Created_at: 2020-10-07 13:09:08 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r500997648  

So this concept assert was commented out. It's because after changing the old strategy to umbrella strategy you'd have to alter the concept check as well. I'm ok with doing it later.

> Username: vissarion  
> Created_at: 2020-10-08 14:51:31 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r501784759  

OK, I will leave to a future PR.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-12 22:46:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-506960662  

📁 include/boost/geometry/strategies/convex_hull/geographic.hpp

```diff
  49 |+                 Spheroid,
  50 |+                 CalculationType
  51 |+             >();
```

> Username: awulkiew  
> Created_at: 2020-10-12 22:46:37 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503570950  

Here spheroid should've been passed (e.g. `base_t::m_spheroid`).

> Username: vissarion  
> Created_at: 2020-10-13 07:27:10 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503724314  

right, done.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-12 22:50:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-506961970  

📁 include/boost/geometry/strategies/convex_hull/spherical.hpp

```diff
  28 |+     typename CalculationType = void
  29 |+ >
  30 |+ class spherical : strategies::detail::spherical_base<RadiusTypeOrSphere>
```

> Username: awulkiew  
> Created_at: 2020-10-12 22:50:00 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503572140  

Radius is not needed in this strategy so it could take only `CalculationType`. There is a version `spherical_base` specialized for `void` for that ocasion, i.e.:  
```  
template <typename CalculationType = void>  
class spherical : strategies::detail::spherical_base<void>  
```

> Username: vissarion  
> Created_at: 2020-10-13 07:29:24 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503726294  

nice, thanks


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-12 22:51:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-506962426  

📁 include/boost/geometry/strategies/geographic/side.hpp

```diff
 123 |                     <calc_t, false, true, false, false, false> inverse_formula;
 124 | 
 125 |+         if (geometry::equals(p, p1) || geometry::equals(p, p2))
```

> Username: awulkiew  
> Created_at: 2020-10-12 22:51:25 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503572574  

`equals` needs geographic/spherical point_in_point strategy.

> Username: awulkiew  
> Created_at: 2020-10-12 22:53:56 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503573789  

Btw, is it a fix for a bug?

> Username: vissarion  
> Created_at: 2020-10-13 08:21:48 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503758980  

>equals needs geographic/spherical point_in_point strategy.  
  
right  
  
>Btw, is it a fix for a bug?  
  
Indeed, this fixes this bug https://github.com/boostorg/geometry/issues/765.


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-12 22:56:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-506964792  

📁 include/boost/geometry/strategies/spherical/side_by_cross_track.hpp

```diff
  76 |+             return 0;
  77 |+         }
  78 |+ 
```

> Username: awulkiew  
> Created_at: 2020-10-12 22:56:53 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503574699  

This doesn't look correct. Azimuth 0 means going north which doesn't mean that the point lies at the same great circle as the segment. Or am I missing something?

> Username: vissarion  
> Created_at: 2020-10-13 08:25:04 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503761265  

This is a leftover from testing this function. I think the whole file could be removed, since this strategy is not used anywhere and `spherical_side_formula` does the same computation more efficiently.

> Username: awulkiew  
> Created_at: 2020-10-15 23:57:37 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r505928746  

>This is a leftover from testing this function.  
  
Ok, so these lines should be removed, right?  
  
> I think the whole file could be removed, since this strategy is not used anywhere and `spherical_side_formula` does the same computation more efficiently.  
  
Well, I don't know. One is using azimuths and the other 3d vectors and AFAIR the accuracy is different in both cases.

> Username: vissarion  
> Created_at: 2020-10-16 08:11:07 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r506152547  

OK, so let's keep it. However, since there is an issue which is similar to the geographic side issue I updated  https://github.com/boostorg/geometry/issues/765 and I propose here a similar fix to the one of the geographic side.


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-12 22:58:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-506965199  

📁 include/boost/geometry/strategy/cartesian/precise_area.hpp

```diff
   8 |+ // http://www.boost.org/users/license.html
   9 |+ 
  10 |+ #ifndef BOOST_GEOMETRY_STRATEGY_CARTESIAN_AREA_ACCURATE_HPP
```

> Username: awulkiew  
> Created_at: 2020-10-12 22:58:10 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503575080  

Macro name does not correspond to file name.

> Username: vissarion  
> Created_at: 2020-10-13 08:26:57 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503762613  

OK this is from PR https://github.com/boostorg/geometry/pull/749 but I am fixing it here.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-12 22:59:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-506965778  

📁 include/boost/geometry/algorithms/detail/convex_hull/graham_andrew.hpp

```diff
 219 |-         // TODO: User-defiend CS-specific side strategy
 220 |-         typename strategy::side::services::default_strategy<cs_tag>::type side;
 218 |+         auto side_strategy = strategy.side();
```

> Username: awulkiew  
> Created_at: 2020-10-12 22:59:56 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503575653  

`auto const`

> Username: vissarion  
> Created_at: 2020-10-13 08:28:02 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503763396  

right


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-12 23:01:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-506966193  

📁 include/boost/geometry/strategies/convex_hull/cartesian.hpp

```diff
  28 |+     static auto side()
  29 |+     {
  30 |+         return strategy::side::side_robust<CalculationType>();
```

> Username: awulkiew  
> Created_at: 2020-10-12 23:01:08 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503576040  

So the default side strategy is robust strategy. Is that correct? Typically we're choosing the fastest as the default. Does it mean that the robust side strategy is also faster?  
  
EDIT: Is it because robust convex_hull is needed in e.g. in triangulation? If that's the case then convex_hull umbrella strategy could define non-robust strategy and triangulation umbrella strategy could define robust one.

> Username: vissarion  
> Created_at: 2020-10-13 08:31:23 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503765650  

> So the default side strategy is robust strategy. Is that correct? Typically we're choosing the fastest as the default. Does it mean that the robust side strategy is also faster?  
  
Yes, I chose to put robust as default one. It seems that it is also faster, see this discussion https://github.com/boostorg/geometry/issues/699  
  
> EDIT: Is it because robust convex_hull is needed in e.g. in triangulation? If that's the case then convex_hull umbrella strategy could define non-robust strategy and triangulation umbrella strategy could define robust one.  
  
No, this is independent from triangulation.


---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-12 23:11:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-506969406  

📁 include/boost/geometry/strategy/cartesian/side_non_robust.hpp

```diff
  32 |+     typename CalculationType = void
  33 |+ >
  34 |+ struct side_non_robust
```

> Username: awulkiew  
> Created_at: 2020-10-12 23:11:27 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503578935  

So with this PR we have 3 cartesian side strategies: `side_by_triangle`, `side_non_robust` and `side_robust` with `side_robust` being the default for convex_hull.  
  
And we also have `strategy::area::cartesian` and `strategy::area::precise_cartesian`. With the latter not defined in convex_hull umbrella strategy and probably only needed for testing.  
  
So my questions are:  
  
1. What's the difference between `side_by_triangle` and `side_non_robust`?  
  
2. Why side is called robust and area called precise? Is this distinction actually important or are these names more or less arbitrary? Shouldn't we think about some consistent naming?  
  
3. Are these robust/precise strategies complementary. I.e. is it the case that they should be used together in algorithms requiring both area and side strategies? Or can they be mixed with non-robust/non-accurate strategies?

> Username: vissarion  
> Created_at: 2020-10-13 08:44:27 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r503774298  

> So with this PR we have 3 cartesian side strategies: `side_by_triangle`, `side_non_robust` and `side_robust` with `side_robust` being the default for convex_hull.  
  
> And we also have `strategy::area::cartesian` and `strategy::area::precise_cartesian`. With the latter not defined in convex_hull umbrella strategy and probably only needed for testing.  
  
Actually proposed in this PR https://github.com/boostorg/geometry/pull/749 while the current one build on top of https://github.com/boostorg/geometry/pull/749  
   
> So my questions are:  
>   
>     1. What's the difference between `side_by_triangle` and `side_non_robust`?  
  
`side_non_robust` is faster but probably less accurate since it does not implement any heuristic it just computes the determinant.   
  
>     2. Why side is called robust and area called precise? Is this distinction actually important or are these names more or less arbitrary? Shouldn't we think about some consistent naming?  
  
I chose robust keyword for computations of predicates (yes/no result) to declare the guarantee that the result will be correct. I use precise keyword for constructions (numeric result) to declare that the result is "more precise" than the more simple computation. Of course in certain cases you can make it "more precise" (and yet slower) if you use multiprecision. We should definitely use some consistent naming. An alternative to the proposal above could be to use the keyword "robust" to whatever is more involved than straightforward floating points computation.   
   
>     3. Are these robust/precise strategies complementary. I.e. is it the case that they should be used together in algorithms requiring both area and side strategies? Or can they be mixed with non-robust/non-accurate strategies?  
  
They can be mixed. You can compute a convex hull with robust side predicate and then compute the area with the non_robust are strategy. They are independent.

> Username: awulkiew  
> Created_at: 2020-10-16 00:01:39 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r505929852  

Ok, the naming makes sense. I only worry that it may be start to be confusing at some point when there are different strategies called robust, precise or accurate. What if there are two different methods of achieving robustness or precision? Would it make sense or even be possible to use some more explicit names, e.g. indicating how the calculation is performed exactly?

> Username: vissarion  
> Created_at: 2020-10-16 08:37:54 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r506175753  

I think for now we can stick to "robust" (for predicates) and "precise" (for constructions). Note that I am also OK with "robust" for all. Then, if at some point we have two different methods of robustness (or "preciseness") for the same problem (which is a bit unlikely at least w.r.t. my current understanding of the BG development) we can think of specialized names.  
  
@barendgehrels what do you think about this PR and the discussed issues on robustness?


---

## Review 12 [Approved]

> Username: awulkiew  
> Created_at: 2020-10-22 19:45:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-515061033  

📁 include/boost/geometry/strategies/convex_hull/cartesian.hpp

```diff
  23 |+ 
  24 |+ template <typename CalculationType = void>
  25 |+ class cartesian : strategies::detail::cartesian_base
```

> Username: awulkiew  
> Created_at: 2020-10-22 19:42:39 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r510412315  

I forgot to mention that the base class should be `public` in order to allow access to its members.

> Username: vissarion  
> Created_at: 2020-10-23 07:48:48 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r510698392  

It is `public`. Am I missing something or you are looking at a older version?

> Username: awulkiew  
> Created_at: 2020-10-23 17:21:53 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r511029496  

No, I don't think so. It should either be:  
```  
class cartesian : public strategies::detail::cartesian_base  
```  
or  
```  
struct cartesian : strategies::detail::cartesian_base  
```

> Username: vissarion  
> Created_at: 2020-10-26 13:04:06 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r511942491  

oh, sure, I misunderstood your first comment


📁 include/boost/geometry/strategies/convex_hull/geographic.hpp

```diff
  29 |+     typename CalculationType = void
  30 |+ >
  31 |+ class geographic : strategies::detail::geographic_base<Spheroid>
```

> Username: awulkiew  
> Created_at: 2020-10-22 19:43:03 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r510412528  

`public`


📁 include/boost/geometry/strategies/convex_hull/spherical.hpp

```diff
  24 |+ 
  25 |+ template <typename CalculationType = void>
  26 |+ class spherical : strategies::detail::spherical_base<void>
```

> Username: awulkiew  
> Created_at: 2020-10-22 19:43:24 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r510412720  

`public`


---

## Comment 13

> Username: vissarion  
> Created_at: 2020-11-03 15:01:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#issuecomment-721181874  

@barendgehrels do you agree to merge this to develop to go to 1.75

---

## Review 14 [Approved]

> Username: barendgehrels  
> Created_at: 2020-11-04 08:53:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/756#pullrequestreview-513603192  

> @barendgehrels do you agree to merge this to develop to go to 1.75  
  
Yes, sorry for the delay. I recently went through this and I'm OK. You can merge it.

📁 include/boost/geometry/algorithms/convex_hull.hpp

```diff
 373 |- 
  24 |+ #include <boost/geometry/algorithms/detail/convex_hull/interface.hpp>
  25 |+ #include <boost/geometry/algorithms/detail/convex_hull/graham_andrew.hpp>
```

> Username: barendgehrels  
> Created_at: 2020-10-21 12:07:07 UTC  
> Updated_at: 2020-12-21 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/756#discussion_r509222594  

It's hard to review this.  
  
If possible, please, make next time first a PR which only moves things (in this case to interface.hpp and to the graham_andrew.hpp>. A move we can trust without diving into all the details.  
  
But now I've no idea what is changed here... (without downloading all new code, finding old/new and using meld)


---
