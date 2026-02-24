# #926 fix compilation of collect vectors when rescaling is turned off [Merged]

> Username: barendgehrels  
> Created at: 2021-10-20 09:39:34 UTC  
> Updated at: 2021-10-29 14:34:52 UTC  
> Merged at: 2021-10-29 14:34:43 UTC  
> Closed at: 2021-10-29 14:34:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/926  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-10-20 09:39:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/926#pullrequestreview-784242349  

📁 include/boost/geometry/algorithms/detail/equals/collect_vectors.hpp

```diff
  69 |+         T,
  70 |+         Geometry,
  71 |+         typename strategy::side::services::default_strategy<CSTag>::type,
```

> Username: barendgehrels  
> Created_at: 2021-10-20 09:39:58 UTC  
> Updated_at: 2021-10-20 09:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r732595966  

This is the fix (plus deleting specialization for triangle), the rest are some minor changes

> Username: awulkiew  
> Created_at: 2021-10-20 13:19:18 UTC  
> Updated_at: 2021-10-20 14:13:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r732768658  

Why is it a problem? I don't see a reason why does it fail to compile.  
  
Furthermore `collected_vector` was specialized for both `side_robust` and `side_by_triangle` strategies to allow using it for both of them and I think it should stay this way. Otherwise it will not be possible to use it with various strategies in the same program.  
  
It could probably be specialized for all cartesian strategies. So:  
```  
  template <typename T, typename Geometry, typename Strategy>  
  struct collected_vector<T, Geometry, Strategy, cartesian_tag>  
```

> Username: barendgehrels  
> Created_at: 2021-10-20 14:43:50 UTC  
> Updated_at: 2021-10-20 14:46:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r732854715  

The problem is that it doesn't compile when rescaling is off, for a (IMO) weird reason  
```  
typename boost::range_value<Collection>::type v(*prev, *it);  
```  
  
```  
../mygeometry/test/boost/geometry/algorithms/detail/equals/collect_vectors.hpp:361:59: error: no matching constructor for initialization of 'typename boost::range_value<vector<collected_vector<double, multi_polygon<polygon<point<double, 2, cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, std::vector, std::allocator>, side_by_triangle<void>, cartesian_tag>, allocator<collected_vector<double, multi_polygon<polygon<point<double, 2, cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, std::vector, std::allocator>, side_by_triangle<void>, cartesian_tag> > > >::type' (aka 'boost::geometry::collected_vector<double, boost::geometry::model::multi_polygon<boost::geometry::model::polygon<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, std::vector, std::allocator>, boost::geometry::strategy::side::side_by_triangle<void>, boost::geometry::cartesian_tag>')  
            typename boost::range_value<Collection>::type v(*prev, *it);  
```  
  
Besides that, to specialize on a strategy sounds really weird to me, why are we doing this? I don't know any other struct doing this (but I didn't look extensively). We specialize on CS Tag, but why on strategy? A side-strategy not even used in this whole file...  
  
The given reason is that it should internally be consistent with Equals -  but a much better solution is then to pass that strategy, as we normally do. And then it could be used (now there are raw calls like `formula::sph_side_value(direction, other.prev)`)

> Username: awulkiew  
> Created_at: 2021-10-20 18:13:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r733030717  

> The problem is that it doesn't compile when rescaling is off, for a (IMO) weird reason  
  
Then we have to see why is it the case and fix that instead.  
  
> Besides that, to specialize on a strategy sounds really weird to me, why are we doing this?  
  
The original reason was consistency (mainly) with spherical and geographic strategies. For spherical side formula the vector or rather segment's direction representation is different than for a formula calculating spherical side with spherical angles. Granted, the proper way would be to implement another kind of strategy for this algorithm which would have to be consistent with side strategy used in other places.  
  
> The given reason is that it should internally be consistent with Equals  
  
Not with equals() but with side strategy used in equals.  
  
> but a much better solution is then to pass that strategy, as we normally do. And then it could be used (now there are raw calls like formula::sph_side_value(direction, other.prev))  
  
I don't understand what are you proposing. To pass side strategy and then use it to calculate direction representation of a segment?

> Username: awulkiew  
> Created_at: 2021-10-20 18:27:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r733040526  

The problem is that the ctors are missing in specialization for `side_by_triangle`, not that `collect_vectors` is specialized. Here is the fix:  
https://github.com/awulkiew/geometry/commit/46eba1c5b2d4e3f0fbf98adc054811341f9ec262

> Username: barendgehrels  
> Created_at: 2021-10-20 20:34:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r733125952  

My point is it should not be specialized on a strategy. I understand that your fix is in line with current implementation, and my fix was too (but different and maybe not good indeed). But the current implementation is wrong. We never specialize on strategy. So we should remove it here too.

> Username: awulkiew  
> Created_at: 2021-10-20 22:10:52 UTC  
> Updated_at: 2021-10-20 22:12:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r733182811  

Yes, I get your point. This implementation was quick solution to the problem of `collect_vectors` using hardcoded cartesian in the past. It was the original design of this optimization of `equals()` for linear geometries. This was in a time when `equals()` had to take intersection strategy and nothing else was permitted. Obviously this was before intersection strategy returning other strategies, not to mention that it was before umbrella strategies. So now there could be a separate strategy for this.  
  
So yes, I agree with you that the design is different than in other parts of the library and it should probably be changed. Now it would be simple, in the past not so much. But this doesn't mean that we have to choose the worst solution to the problem. We should rather do something less wrong (even if not fully correct) or even something correct (though it's out of scope of this PR), so one of these:  
- implementing missing constructors,  
- taking any `typename Strategy` in cartesian specialization so there is only one specialization and it can be instantiated for all cartesian strategies,  
- implementing `collect_vectors` strategies (or some alternative) and adding getters for them to `relate` umbrella strategies (this is probably out of scope of this PR so maybe an issue should be created?),  
- removing the whole `collect_vectors` entirely since it's only an optimization and enabling version using `get_turns` (I don't think we should do this but it would be perfectly correct solution, I'd say even the best and the most consistent across the whole library).  
  
Btw, even formulas in cartesian `collect_vector` may not be consistent with the formulas in all cartesian side strategies (so this is the case not only for spherical and geographic). In fact `side_robust` may require something else than `side_by_triangle` here, some robust computation of vectors/directions. @vissarion are formulas used in cartesian `collect_vectors` consistent with robust version of side?

> Username: barendgehrels  
> Created_at: 2021-10-21 16:03:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r733829152  

Okay, I agree with you that, for now, the context of this PR, the fix should be small. Your second option sound good to me, I’ll try that (later) and update the PR.  
  
So there is equals-by-relate which indeed does not need collect vectors at all. So we can just remove collect vectors? Why shouldn’t we do it, if it is the perfectly correct and best and most consistent solution?

> Username: awulkiew  
> Created_at: 2021-10-21 17:27:09 UTC  
> Updated_at: 2021-10-21 17:27:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r733901089  

> Why shouldn’t we do it, if it is the perfectly correct and best and most consistent solution?  
  
AFAIR `collect_vectors` version is faster. It doesn't have to calculate turns/intersection points. And we tend to prefer performance over maintenance cost or consistency in Boost.Geometry.

> Username: barendgehrels  
> Created_at: 2021-10-23 11:51:42 UTC  
> Updated_at: 2021-10-23 11:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r734961470  

@awulkiew thanks for your useful info and opinions. With this I looked more into the code, and decided to change it. This is a bit like your option 2, but different. The decision to use collect_vectors is now not taken by that specialization iself, but by a small helper structure in the equals implementation.

> Username: barendgehrels  
> Created_at: 2021-10-23 12:00:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r734962331  

I realize we still specialize on strategy (in that helper structure `use_collect_vectors`), but that can probably go too. Because I noticed there were no unit tests at all for this (equals on spherical or geographic) and I added a small test. But it fails (using spherical side formula, so using collect vectors) - and it succeeds (using relate).   
So this goes (partially) to your last option: avoid using `collect_vectors` for anything but cartesian. I would be in favor of that. This simplifies `use_collect_vectors` a bit further (or it can go, use the tag in the `condition_t` directly)

> Username: awulkiew  
> Created_at: 2021-10-23 14:19:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r734975476  

@barendgehrels Ok, but having 2 templates doing the same thing is a bit redundant. I don't understand the rationale behind this change but I'm not against it. If you prefer this then I propose to remove `CSTag` from `collect_vector` because as I mentioned these implementations are tweaked for specific side strategies (not only coordinate systems) and we should think about them more or less as of strategies. So I propose to rename them to: `collect_vectors_cartesian` or `collect_vector_by_triangle`, `collect_vector_ssf` and `collect_vector_geographic`. Then to define them in your new specializations.  
  
Regarding the tests, all of these tests are for `spherical_equatorial`:  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/equals/equals_sph.cpp  
For geographic indeed we need them.  
  
I don't understand the reasoning that if there is a bug somewhere then we can remove the algorithm entirely. AFAIU equals/relate() also fails for some case (that looks simple as well, I forgot to report it, I'll find it) and collect vectors don't fail. It doesn't mean that we can remove one or the other. We may choose to remove it after deciding that we do not need performance when we have to pay the maintenance cost or that we prefer consistency, etc. But not because there may be a bug somewhere and we don't want to find it.

> Username: awulkiew  
> Created_at: 2021-10-23 15:22:28 UTC  
> Updated_at: 2021-10-23 15:22:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r734981757  

> So this goes (partially) to your last option: avoid using collect_vectors for anything but cartesian.  
  
Have in mind that this implementation may not be consistent with all cartesian side strategies since `side_robust` gives different results than `side_by_triangle`. @vissarion ?

> Username: awulkiew  
> Created_at: 2021-10-23 18:07:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r734997913  

@barendgehrels Here is the fix for this spherical case:  
https://github.com/awulkiew/geometry/commit/97f1bc11cf053bd5a4fc6e80e8f8441ed56548d3

> Username: barendgehrels  
> Created_at: 2021-10-26 15:42:14 UTC  
> Updated_at: 2021-10-26 15:42:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r736680000  

> I don't understand the reasoning that if there is a bug somewhere then we can remove the algorithm entirely.   
  
I referred to what you were saying: "removing the whole collect_vectors entirely since it's only an optimization"

> Username: barendgehrels  
> Created_at: 2021-10-26 15:49:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r736686513  

> Ok, but having 2 templates doing the same thing is a bit redundant  
  
They are completely different and have different purposes. Specializing a structure on a strategy which is not used is not acceptable for me. Now it is clear what is the purpose, and weird thing is gone.

> Username: barendgehrels  
> Created_at: 2021-10-26 15:50:56 UTC  
> Updated_at: 2021-10-26 15:50:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r736688065  

> Have in mind that this implementation may not be consistent with all cartesian side strategies since side_robust gives different results than side_by_triangle  
  
I completely don't get this - the side strategy isn't used in that function. So how can it be inconsistent...

> Username: barendgehrels  
> Created_at: 2021-10-26 15:52:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r736689607  

> and we should think about them more or less as of strategies  
  
What's defined here is a structure (containing coordinates), it's not a strategy. It's a vector (at least it's called like that). I think it's fine the way it is now

> Username: barendgehrels  
> Created_at: 2021-10-26 15:56:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r736693163  

> Regarding the tests, all of these tests are for spherical_equatorial: equals_sph.cpp  
  
Thanks - but that file is not tested (which is why I missed it, sorry)  
```  
    [ run equals.cpp             : : : : algorithms_equals ]  
    [ run equals_multi.cpp       : : : : algorithms_equals_multi ]  
    [ run equals_on_spheroid.cpp : : : : algorithms_equals_on_spheroid ]  
```

> Username: barendgehrels  
> Created_at: 2021-10-26 17:31:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r736770939  

> Here is the fix for this spherical case: awulkiew@97f1bc1  
  
Thanks! Do you want to include it in this PR?

> Username: awulkiew  
> Created_at: 2021-10-26 18:16:29 UTC  
> Updated_at: 2021-10-26 18:21:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r736804580  

> I completely don't get this - the side strategy isn't used in that function. So how can it be inconsistent...  
  
An optimization only makes sense if it is faster and it gives the same results as non-optimized version. Non-optimized version would use side strategy. If `collect_vectors` is not consistent with this side strategy then the latter requirement is not met. In other words the result of `equals()` should be the same as the result of `relate()` with de9im equals mask. `intersects()` should generate the same geometry for equal geometries, `difference()` should generate empty geometry, etc. This is why it has to be consistent with side strategy (and intersection strategy) which is used in all of the above. And as you found out even cartesian side strategies do not give the same results for some edge cases.  
  
> What's defined here is a structure (containing coordinates), it's not a strategy. It's a vector (at least it's called like that). I think it's fine the way it is now  
  
Yes, my point was that this is a piece of the code that is coordinate-system specific. We call things like that strategies (when they have correct structure, `apply()` method etc.). I proposed to remove the `CSTag` because `collect_vector` shouldn't be used in CS-context as it is defined now but in the context of specific side strategy.  
  
> Thanks - but that file is not tested (which is why I missed it, sorry)  
  
I also wasn't aware of that, sorry. I thought it's tested. I wonder if there are some problems with it or was this just an ommission by mistake.  
  
> Thanks! Do you want to include it in this PR?  
  
Not neccessarily. I could fix this after this PR is merged, create a PR for your PR or you could just copy the fix. Whatever you think is the most convenient.  
  
Btw, this fix makes `collect_vectors` consistent with `ssf` strategy because in this strategy the cross product (normal vector) is not normalized when side value is calculated. Here the direction has to be normalized to be comparable with other directions.  
  
In cartesian version it is also normalized and these normalized vectors are compared by coordinate with epsilon when collinerality is checked:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/equals/collect_vectors.hpp#L151-L152  
while in `side_by_triangle` the determinant is calculated:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategy/cartesian/side_by_triangle.hpp#L109  
and the result compared with `0`. In `side_robust` yet another formula is used:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategy/cartesian/side_robust.hpp#L126  
which is implemented here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/precise_math.hpp#L366  
I'd expect that all of these formulas could give different results in some edge cases.  
  
So it is possible that after applying the fix spherical version is more consistent with corresponding side strategy, not the cartesian one. Though it is probably harder to find failing cartesian case.

> Username: barendgehrels  
> Created_at: 2021-10-27 09:47:44 UTC  
> Updated_at: 2021-10-27 09:48:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737296743  

@awulkiew thanks for your clear answer!  
  
> An optimization only makes sense if it is faster and it gives the same results as non-optimized version  
  
OK, now I get it. But it's a bit weird. The two approaches should ideally give the same results but there might be FP-issues. Sometimes they might differ for the robust strategy. Sometimes for the triangle strategy. I can't predict that. We've not millions of tests so we actually just don't know. We (at this moment) use both in the code (robust for rescaling, triangle for non-rescaling) though that should be temporarily. In that sense my earlier fix was right (but I'm glad it's gone and replaced).  
  
> I proposed to remove the CSTag because collect_vector shouldn't be used in CS-context as it is defined now but in the context of specific side strategy.  
  
OK, I could try that. But then indeed we need another way to select the right version of collect_vectors. I understand your strategy-approach now better. But then we should split the storage (independent) from the calculation (strategy). That's actually better, probably, but beyond the scope of this PR.  
  
> I thought it's tested. I wonder if there are some problems with it or was this just an ommission by mistake.  
  
I'll try to enable it  
  
> or you could just copy the fix.   
  
That is probably convenient because I will enable the sph test.

> Username: barendgehrels  
> Created_at: 2021-10-27 10:53:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737345968  

> > Here is the fix for this spherical case: [awulkiew/geometry@97f1bc1](https://github.com/awulkiew/geometry/commit/97f1bc1)  
>   
> Thanks! Do you want to include it in this PR?  
  
The fix works correctly. Thanks again.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-10-20 09:41:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/926#pullrequestreview-784243889  

📁 include/boost/geometry/algorithms/detail/equals/collect_vectors.hpp

```diff
 378 |-         collection_size_t collected_count = boost::size(collection) - c_old_size;
 379 |-         if ( collected_count > 1 )
 368 |+         if (boost::size(collection) > c_old_size + 1)
```

> Username: barendgehrels  
> Created_at: 2021-10-20 09:41:28 UTC  
> Updated_at: 2021-10-20 09:41:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r732597627  

I rewrote the condition to avoid the extra type and the subtraction on a size_t


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2021-10-23 12:03:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/926#pullrequestreview-787386105  

📁 test/algorithms/equals/equals_on_spheroid.cpp

```diff
 237 |+ #if defined(BOOST_GEOMETRY_TEST_FAILURES)
 238 |+ // This version uses collect_vectors (because its side
 239 |+ // strategy is spherical_side_formula) and fails
```

> Username: barendgehrels  
> Created_at: 2021-10-23 12:03:09 UTC  
> Updated_at: 2021-10-23 12:03:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r734962546  

So, with `relate` it succeeds...

> Username: barendgehrels  
> Created_at: 2021-10-27 11:14:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737360006  

Now it's fine in all cases


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2021-10-23 12:07:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/926#pullrequestreview-787386411  

📁 include/boost/geometry/algorithms/detail/equals/collect_vectors.hpp

```diff
 149 | // Compatible with spherical_side_formula which currently
 150 | // is the default spherical_equatorial and geographic strategy
 151 | // so CSTag is spherical_equatorial_tag or geographic_tag
```

> Username: barendgehrels  
> Created_at: 2021-10-23 12:07:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r734962844  

I should update this comment.  
But maybe we will remove the whole specialization for spherical/geo (especially if they don't work for the most simple cases)...

> Username: vissarion  
> Created_at: 2021-10-27 10:37:03 UTC  
> Updated_at: 2021-10-27 10:37:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737334368  

Is this the case here? Does `collected_vector` for non-cartesian returns wrong results? It seems it fails here https://github.com/boostorg/geometry/pull/926/files#diff-6898dddc1f86c9a558e1a3871bf25eebfa1b492dcb7a0e7c7de1c86a78e400b5R238   
So if it returns wrong results I agree in removing that specialization.

> Username: barendgehrels  
> Created_at: 2021-10-27 10:54:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737346219  

It's now fixed by @awulkiew 's change


---

## Comment 5

> Username: barendgehrels  
> Created_at: 2021-10-26 15:59:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#issuecomment-952083587  

In summary, I think the new solution is better, clearer and shorter than previous one, and it should work identical as before (I mean: the same code is called for the same input - and additionally it fixes the issue I encountered when rescaling is not there). I've to clean up a few comments and I propose to then merge it.

---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-26 18:28:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/926#pullrequestreview-789770278  

📁 include/boost/geometry/algorithms/detail/equals/implementation.hpp

```diff
 250 |-             > enable_relate_type;
 258 |+                 use_collect_vectors<side_strategy,
 259 |+                      typename cs_tag<Geometry1>::type>::value,
```

> Username: awulkiew  
> Created_at: 2021-10-26 18:28:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r736813114  

`typename Strategy::cs_tag` should probably have been passed here instead so this would depend on the umbrella strategy. In case a user passed e.g. geographic point with spherical umbrella strategy or vice versa.  
  
Btw, there is a formatting issue here too. I have no problems with formatting templates like that but we have a guideline for that. Would you like to relax it?

> Username: barendgehrels  
> Created_at: 2021-10-27 09:50:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737299246  

I'll pass that Strategy::cs_tag, better indeed  
And I'll fix the formatting, it was an error

> Username: barendgehrels  
> Created_at: 2021-10-27 11:15:36 UTC  
> Updated_at: 2021-10-27 11:15:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737360901  

:heavy_check_mark:


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-26 18:35:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/926#pullrequestreview-789777071  

📁 include/boost/geometry/algorithms/detail/equals/implementation.hpp

```diff
 239 |+ {
 240 |+   static constexpr bool value = false;
 241 |+ };
```

> Username: awulkiew  
> Created_at: 2021-10-26 18:35:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r736817957  

This is probably not needed. There are two scenarios:  
1. As it is now, `CSTag` is taken from `Geometry`. This means that a user passed spherical umbrella strategy for geographic geometry. We shouldn't overwrite this and we should use spherical `collect_vectors` as the user intended.  
2. If `Strategy::cs_tag` is passed below as I think it should've then this would mean that geographic umbrella strategy defined spherical side strategy which should not happen.

> Username: barendgehrels  
> Created_at: 2021-10-27 11:15:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737360688  

Thanks. I'm passing `Strategy::cs_tag` now. I believed I handled all comments.


---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2021-10-27 11:18:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/926#pullrequestreview-790495143  

📁 include/boost/geometry/algorithms/detail/equals/collect_vectors.hpp

```diff
 240 |+     using base_point_type
 241 |+         = model::point<T, 2, cs::spherical_equatorial<geometry::degree>>;
 242 |+     using base_type = collected_vector_spherical<T, base_point_type>;
```

> Username: barendgehrels  
> Created_at: 2021-10-27 11:18:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737362581  

I think should have different types, so I changed it.  
But it is, and was, never tested (we normally don't use / test polar yet).   
Alternatively we could remove it, but the approach itself might work.  
  
Note that to use this polar version, it would need some changes in `equals/implementation.hpp`


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-27 13:15:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/926#pullrequestreview-790622345  

📁 include/boost/geometry/algorithms/detail/equals/implementation.hpp

```diff
 231 |+ struct use_collect_vectors
 232 |+ {
 233 |+   static constexpr bool value = false;
```

> Username: awulkiew  
> Created_at: 2021-10-27 13:15:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737454308  

Minor detail, 2 spaces instead of 4.

> Username: barendgehrels  
> Created_at: 2021-10-28 07:30:10 UTC  
> Updated_at: 2021-10-28 07:30:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r738095181  

:heavy_check_mark:


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-27 13:23:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/926#pullrequestreview-790633186  

📁 include/boost/geometry/algorithms/detail/equals/implementation.hpp

```diff
 197 |+                     >,
 198 |+                 collected_vector_cartesian<calculation_type>
 199 |+             >;
```

> Username: awulkiew  
> Created_at: 2021-10-27 13:23:42 UTC  
> Updated_at: 2021-10-27 13:23:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r737462195  

So the correctness of this depends on how the `use_collect_vectors` and umbrella strategy are specialized. This is ok I guess but it means that we have to keep two places in the code synchronized. We could think about some different mechanism allowing to define corresponding `collected_vector_xxx` for given side strategy and `cs_tag` in one place instead of two places. But what there is now is ok for now. I may propose it later in a different PR.

> Username: barendgehrels  
> Created_at: 2021-10-29 14:34:02 UTC  
> Updated_at: 2021-10-29 14:34:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/926#discussion_r739287528  

As I wrote earlier, due to FP issues you might get rare differences in collect/relate in either triangle or robust strategy. That is not solvable. So it will never be 100% consistent. So for cartesian we can always use collect-vectors, regardless of strategy.  
  
For spherical I've less overview but I guess it might be the same there. And then the optimized version would be more preferred too, provided that it works correctly for spherical (but why shouldn't it?). And if it doesn't work correctly - maybe we can then just remove that version?  
  
Anyway, thanks again for review, comments, views and approval and I'll merge this, and I'm open to revising it later, of course.


---
