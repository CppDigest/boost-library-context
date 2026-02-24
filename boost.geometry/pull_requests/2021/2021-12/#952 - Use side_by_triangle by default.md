# #952 Use side_by_triangle by default [Merged]

> Username: barendgehrels  
> Created at: 2021-12-15 15:50:16 UTC  
> Updated at: 2021-12-30 10:32:45 UTC  
> Merged at: 2021-12-30 10:16:49 UTC  
> Closed at: 2021-12-30 10:16:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/952  

This PR (edited):  
  
- removes the define such that the new `side_triangle` is now always the default strategy ( (to be researched further why the robust fails for some obvious cases, as discussed with @vissarion ).  
- instead, for the convex_hull, the umbrella_strategy defines still `side_robust`  
  
After this PR:  
- convex hull passes again, when `NO_ROBUSTNESS` is defined  
- get_turns_linear_linear passes again, when `NO_ROBUSTNESS` is defined  
- there is less dependency on the define `BOOST_GEOMETRY_USE_RESCALING`

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-15 17:17:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/952#pullrequestreview-833112987  

📁 include/boost/geometry/strategies/relate/cartesian.hpp

```diff
  41 | 
  42 |- template <typename CalculationType = void>
  42 |+ template <typename CalculationType = void, bool IsSetOperation = false>
```

> Username: awulkiew  
> Created_at: 2021-12-15 17:17:12 UTC  
> Updated_at: 2021-12-15 17:17:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r769831378  

I'm against this change. If you think that set operations need different umbrella strategy then you should create another one instead of adding template argument that doesn't exist in any other umbrella strategy.  
  
One of the reasons for umbrella strategies to exist is to have intuitive and consistent interface. This unfortunately also means: not adding arguments because it's convenient.

> Username: awulkiew  
> Created_at: 2021-12-15 17:19:10 UTC  
> Updated_at: 2021-12-15 17:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r769832922  

Btw, what with the general umbrella strategy `strategies::cartesian<>` that can be passed into all algorithms?  
Which side strategy should be used there?  
Are relational and set operations different so they need different side strategies?  
Does it mean that the results of relational and set operations are not consistent?


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-15 17:25:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/952#pullrequestreview-833121783  

📁 include/boost/geometry/strategies/relate/cartesian.hpp

```diff
 407 | 
 408 |+ 
 409 |+ namespace strategies { namespace set_operations { namespace services {
```

> Username: awulkiew  
> Created_at: 2021-12-15 17:25:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r769837430  

This is related to a change that I haven't done because I didn't want to make my PRs even bigger. The intention of namespaces of umbrella strategies is to have names corresponding to algorithms. This way the user knows exactly which umbrella strategy can be used. For this reason `set_operations` should be replaced with four namespaces `difference`, `intersection`, `sym_difference` and `union_`. But as I mentioned above this change looks wierd because it means that set and relational operations use different side strategies and therefore can give inconsistent results.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-15 17:27:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/952#pullrequestreview-833124018  

📁 include/boost/geometry/strategies/spherical/intersection.hpp

```diff
 152 |         apply(UniqueSubRange1 const& range_p, UniqueSubRange2 const& range_q,
 152 |-               Policy const&)
 153 |+               Policy const&, SideStrategy const&)
```

> Username: awulkiew  
> Created_at: 2021-12-15 17:27:00 UTC  
> Updated_at: 2021-12-15 17:27:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r769839069  

So side strategy is not used in all intersection strategies. Only in cartesian one? So side strategy is passed here it is ignored? This is inconsistent. But I don't understand why is this needed at all (see other comment).


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-15 17:48:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/952#pullrequestreview-833147526  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 303 |-               Policy const& policy)
 304 |+               Policy const& policy,
 305 |+               SideStrategy const& side)
```

> Username: awulkiew  
> Created_at: 2021-12-15 17:48:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r769855916  

Is the intention to allow to pass various side strategies in the same algorithm? Or is this changed to allow defining side strategy in one place only, i.e. in umbrella strategy and pass it into all strategies using it? If it's the former then this can lead to inconsistencies. If it's the latter then I don't understand why isn't it done for other coordinate systems too?  
  
If the purpose is to "play" with the cartesian strategy then why not passing the side strategy as class template argument? This way the responsibility for the consistency of the strategies will rest only on the umbrella strategy.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-15 17:54:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/952#pullrequestreview-833153326  

📁 include/boost/geometry/strategies/relate/services.hpp

```diff
  65 |+     typename CSTag2 = typename geometry::cs_tag<Geometry2>::type
  66 |+ >
  67 |+ struct default_strategy
```

> Username: awulkiew  
> Created_at: 2021-12-15 17:54:25 UTC  
> Updated_at: 2021-12-15 17:56:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r769860033  

I haven't chacked this out but I think it is not required to define this struct and its specializations. AFAIU the only thing you need is template alias for `strategies::relate::services::default_strategy`:  
```  
template  
<  
    typename Geometry1,  
    typename Geometry2,  
    typename CSTag1 = typename geometry::cs_tag<Geometry1>::type,  
    typename CSTag2 = typename geometry::cs_tag<Geometry2>::type  
>  
using default_strategy = strategies::relate::default_strategy<Geometry1, Geometry2, CSTag1, CSTag2>;  
```  
  
or rather four aliases, one for each set operation (see other comment above, about the namespace).


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-15 17:55:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/952#pullrequestreview-833154039  

📁 include/boost/geometry/strategies/relate/spherical.hpp

```diff
 373 |+ 
 374 |+ template <typename Geometry1, typename Geometry2>
 375 |+ struct default_strategy<Geometry1, Geometry2, spherical_tag, spherical_tag>
```

> Username: awulkiew  
> Created_at: 2021-12-15 17:55:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r769860512  

AFAIU these are not needed (see above).


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-15 17:55:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/952#pullrequestreview-833154577  

📁 include/boost/geometry/strategies/relate/geographic.hpp

```diff
 352 |+ 
 353 |+ template <typename Geometry1, typename Geometry2>
 354 |+ struct default_strategy<Geometry1, Geometry2, geographic_tag, geographic_tag>
```

> Username: awulkiew  
> Created_at: 2021-12-15 17:55:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r769860887  

AFAIU this is not needed (see below).


---

## Comment 8

> Username: barendgehrels  
> Created_at: 2021-12-15 18:30:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-995060231  

Answering (in general) to the questions of @awulkiew  
- no, I don’t think that set operations need a different umbrella strategy.  However, it’s better than the define. The point is: the `side_robust` side strategy isn’t as good as I expected. As soon as rescaling is turned off, it breaks even the most simple cases. I don’t know yet how exactly.@vissarion  is investigating that. For that I first added that define (making `side_by_triangle` the default if rescaling is turned off). But that breaks other cases (if rescaling is turned off), for example convex hull. Therefore I changed this, to make the change more limited. And I think it’s good, but it’s temporary.  
- yes, I think passing the side strategy to the intersection strategy is a good change. Because before, it just got it from the default_strategy service. But it should be passed from the umbrella strategy, because that one specifies it. And yes, we pass all strategies via methods because they might have parameters (such as earth radius etc). So I think this change is exactly as it should be.  
- and yes, the geographic intersection and spherical don’t use that argument, but it should be passed for consistency, of course. Otherwise things don’t compile :-)

---

## Comment 9

> Username: awulkiew  
> Created_at: 2021-12-15 18:44:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-995070527  

> no, I don’t think that set operations need a different umbrella strategy. However, it’s better than the define.  
  
Ok, but this define was added by you before right? If `side_robust` causes problems then I propose to simply go back to `side_by_triangle` in both set and relational operations. Of course this can be investigated. And only after we know the results of this investigation we can change the strategy.  
  
If some cases work with one side strategy and other cases with other one are you sure that they can be simply used interchangeably inside this particular intersection strategy? My guess would rather be that `side_robust` needs corresponding `intersection_robust`.  
  
> Because before, it just got it from the default_strategy service.  
  
Yes, but this was added by you before wasn't it? Originally side strategy was defined inside intersection strategy.  
  
> geographic intersection and spherical don’t use that argument, but it should be passed for consistency  
  
If this is only used by cartesian strategy it could also be taken as template argument. Or there could be a separate intersection strategy. I don't understand why do we assume that it's fine to pass arbitrary side strategies to intersection strategies.

---

## Comment 10

> Username: vissarion  
> Created_at: 2021-12-16 10:34:11 UTC  
> Updated_at: 2021-12-16 10:34:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-995645127  

I agree to switch back to `side_by_triangle` in both set and relational operations and investigate more.   
  
What I currently understand as the issue of `side_robust` is the following: if the input is a set of rational numbers and we compute the result with arbitrary precision then this result will defer from the one we get if you transform the input in floating point numbers and compute the side with `side_robust`. Note that the error comes from the transformation to floating points not by the computation of side.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2021-12-22 09:43:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999433179  

OK, I'll create another PR to go back to `side_by_triangle` - because that will have the same effect.  
Thanks for your input.  
  
Still, some of these changes I want to keep, for example passing side-strategy to intersection. That's really needed and I don't understand why it's not done before. But not problem, we can do it now. Even if it's not really used (by spherical/geographical), it should be passed because it's used by cartesian, and all the strategies should look the same (not from esthetic viewpoint, but otherwise it doesn't compile :-) )

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2021-12-22 09:50:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999437946  

> > no, I don’t think that set operations need a different umbrella strategy. However, it’s better than the define.  
>   
> Ok, but this define was added by you before right? If `side_robust` causes problems then I propose to simply go back to `side_by_triangle` in both set and relational operations. Of course this can be investigated. And only after we know the results of this investigation we can change the strategy.  
>   
> If some cases work with one side strategy and other cases with other one are you sure that they can be simply used interchangeably inside this particular intersection strategy? My guess would rather be that `side_robust` needs corresponding `intersection_robust`.  
>   
> > Because before, it just got it from the default_strategy service.  
>   
> Yes, but this was added by you before wasn't it? Originally side strategy was defined inside intersection strategy.  
>   
> > geographic intersection and spherical don’t use that argument, but it should be passed for consistency  
>   
> If this is only used by cartesian strategy it could also be taken as template argument. Or there could be a separate intersection strategy. I don't understand why do we assume that it's fine to pass arbitrary side strategies to intersection strategies.  
  
I don't understand why it's not fine... Of course it should take the side-strategy from the umbrella-strategy, that's crystal-clear to me...  
Ever, indeed, the Cartesian used side-by-triangle (hard-coded), I don't remember when it was changed and by who. It's probably not so important.  
I don't know the geographic intersection so well - but I think, and I just looked, that it should use the passed strategy too, instead of this for example: `azimuth_side_value`. But that can be fixed later if necessary.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2021-12-22 09:57:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999441943  

Or, actually, most changes will be the same so I reopen this and change the name.  
  
Changes still to be pushed.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2021-12-22 12:58:11 UTC  
> Updated_at: 2021-12-22 13:41:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999557805  

*(restored your original answer)*  
>    Still, some of these changes I want to keep, for example passing side-strategy to intersection.  
  
Changes should be made to solve a problem, not because they can be made. What problem does this one solve? Because I think it only introduces complexity and doesn't solve any problem.  
  
>    That's really needed and I don't understand why it's not done before.  
  
Why is it needed? Side strategy was a member of intersection strategy only because someone made this implementation choice. It might not have been used. A formula might have been used instead. There might have been no need for it at all. Is the use of side strategy a feature of IntersectionStrategy concept? Is it mathematically guaranteed that intersection of two segments has to use point-segment side computation? @vissarion you looked into that. I don't think so (unless I'm wrong) and therefore side strategy should not be a part of the interface of IntersectionStrategy. Even a cartesian intersection strategy could probably have been implemented differently, without any side strategy used internally. A good candidate would probably be one using precise arithmetic. Who said that a side strategy would be needed for that?  
  
If you need different intersection strategy you could create it. Passing side strategy to all existing strategies is not required for that.  
  
If side strategy really has to be passed into one implementation of intersection strategy it should be passed a template argument as it is already done in the library e.g. for distance strategies.  
  
Furthermore practice shows that there are places in the library where various strategies are not correctly taken from umbrella strategy but created by hand (probably becasue that's convenient at the moment). What you're proposing would require to pass 2 strategies instead of one, a good place to take shortcuts for convenience.  
  
>    it should be passed because it's used by cartesian, and all the strategies should look the same (not from esthetic viewpoint, but otherwise it doesn't compile  
  
It doesn't compile only because of the way how you designed it. It's obvious that if you pass a dummy parameter somewhere then a function has to take it. Without the parameter it would compile just fine.  
  
If something is needed by one specific implementation of intersection strategy (this is not even a matter of CS but one specific implementation) then it should be a part of this implementation, not the whole Concept. It should not be propagated into all other implementations.  
  
>    Of course it should take the side-strategy from the umbrella-strategy, that's crystal-clear to me...  
  
Not really. The strategies defined in umbrella strategy has to be consistent with each other, that's it. There is no other requirement. They don't have to take other strategies. If we at some point choose to use some strategy in another specific implementation of another strategy then it is not required to change the interfaces of all existing strategies, etc.  
  
>    Ever, indeed, the Cartesian used side-by-triangle (hard-coded), I don't remember when it was changed and by who. It's probably not so important.  
  
Probably recently by @vissarion .  
  
This is not a matter of cartesian vs other CSes. It's a matter of this specific implementation of intersection strategy. It could be implemented differently without side strategy at all.  
  
>    I don't know the geographic intersection so well - but I think, and I just looked, that it should use the passed strategy too, instead of this for example: azimuth_side_value. But that can be fixed later if necessary.  
  
I don't remember but let's assume that sides are calculated there. The answer is still no. It's because sides can be calculated differently and they are not interchangeable. E.g. a spherical intersection strategy using ECEF 3d vectors should internally calculate sides using the same method. Another intersection strategy could use spherical formulas and sides should've been calculated the same way (and probably reuse some of the variables). In geographic you could model segments with great ellipses, loxodromes or geodesics. They are not interchangeable. The correctness of the intersection strategy should not depend on the caller's ability to pass the correct side strategy. This way we can be sure that the intersection strategy gives correct results without thinking about sides.  
  
There is a matter of consistency of intersection strategy and side strategy in algorithms that can use both of them. But this is a different story and umbrella strategy is for this. Not only side and intersection has to be consistent with each other but all strategies used in an algorithm.  
  
We could of course rethink and redesign all of the strategies. Because various strategies use other strategies internally. I'm strongly against doing it partially only in one place. Using one approach for one set of strategies and then another approach for other set, and then yet another one, etc. because that's convenient at the moment.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2021-12-22 13:36:37 UTC  
> Updated_at: 2021-12-22 13:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999582543  

> > > Still, some of these changes I want to keep, for example passing side-strategy to intersection.  
>   
> > Changes should be made to solve a problem, not because they can be made. What problem does this one solve? Because I think it only introduces complexity and doesn't solve any problem.  
>   
> The `intersection` from `cartesian` should **NOT** use the `side::default_strategy` because the user can pass an umbrella strategy containing another strategy. It's crystal clear, and I actually don't understand why it's not done yet by adding the robustness strategies.  
>   
> However, because you're not convinced yet, and because it (now we reverted the rest) is not necessarily part of this PR anymore, I'll take it out.  
>   
> > If side strategy really has to be passed into one implementation of intersection strategy it should be passed a template argument as it is already done in the library e.g. for distance strategies.  
>   
> I already answered this :-) The side strategy can contain member variables such as earth radius.  
  
Oops, excuse me, I edited your comment (didn't know it was possible) instead of replying it...  
**edit**: restored, my answer now here.

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2021-12-22 13:48:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999589948  

:heavy_check_mark:   
So passing the side-strategy is undone, it's not part of this PR anymore.  
Small side-effect: the test I added in this PR now fails exactly because of that reason. That one is still there (now only tested for failures)

---

## Comment 17

> Username: awulkiew  
> Created_at: 2021-12-22 14:12:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999606314  

> The intersection from cartesian should NOT use the side::default_strategy  
  
Yes.  
  
> because the user can pass an umbrella strategy containing another strategy. It's crystal clear, and I actually don't understand why it's not done yet by adding the robustness strategies.  
  
The user can define inconsistent strategies in umbrella strategy, yes. The user may also define wrong side strategy in umbrella strategy which would then be passed into the inconsistent intersection strategy. Furthermore this side strategy may be inconsitent with the one used in winding strategy. The problem stays the same.  
  
I understand what you wanted to do and indeed this is an issue but the solution you proposed is a partial one.  
  
In general any strategy may use any number of other strategies which in turn may use any number of yet other strategies. It's the responsibility of umbrella strategy to define consistent strategies. An alternative would be to pass umbrella strategy into all legacy strategies as it is the case with algorithms. And even with that the strategies defined in umbrella strategies would have to be consistent with each other.  
  
> However, because you're not convinced yet, and because it (now we reverted the rest) is not necessarily part of this PR anymore, I'll take it out.  
  
We could think about redesigning strategies, but all of them in the whole library. Until now if a strategy had to be passed into another strategy this was done in the constructor with type defined as class template argument. Here it's passed as function argument. AFAIU this was not required since only one side strategy is used in cartesian intersection strategy.

---

## Comment 18

> Username: vissarion  
> Created_at: 2021-12-22 15:29:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999660909  

Regarding the connection between intersection and side strategies, they could use similar computations like determinant computations but they should be independent. That is, you should not, in my opinion, pass the one strategy to another.

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2021-12-22 15:54:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999682760  

> Regarding the connection between intersection and side strategies, they could use similar computations like determinant computations but they should be independent. That is, you should not, in my opinion, pass the one strategy to another.  
  
And use one strategy from another? This is done now.

---

## Comment 20

> Username: barendgehrels  
> Created_at: 2021-12-22 16:08:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999692784  

> > The intersection from cartesian should NOT use the side::default_strategy  
>   
> Yes.  
>   
> > because the user can pass an umbrella strategy containing another strategy. It's crystal clear, and I actually don't understand why it's not done yet by adding the robustness strategies.  
>   
> The user can define inconsistent strategies in umbrella strategy, yes. The user may also define wrong side strategy in umbrella strategy which would then be passed into the inconsistent intersection strategy. Furthermore this side strategy may be inconsitent with the one used in winding strategy. The problem stays the same.  
  
To be honest, I don't see why in this case this creates an inconsistency... IMO using two different side strategies (as can be done now) is inconsistent.  
  
>   
> I understand what you wanted to do and indeed this is an issue but the solution you proposed is a partial one.  
>   
> In general any strategy may use any number of other strategies which in turn may use any number of yet other strategies. It's the responsibility of umbrella strategy to define consistent strategies. An alternative would be to pass umbrella strategy into all legacy strategies as it is the case with algorithms. And even with that the strategies defined in umbrella strategies would have to be consistent with each other.  
>   
> > However, because you're not convinced yet, and because it (now we reverted the rest) is not necessarily part of this PR anymore, I'll take it out.  
>   
> We could think about redesigning strategies, but all of them in the whole library. Until now if a strategy had to be passed into another strategy this was done in the constructor with type defined as class template argument. Here it's passed as function argument. AFAIU this was not required since only one side strategy is used in cartesian intersection strategy.  
  
Actually I was not aware that, at this moment, still, the `oriented_winding` strategy (and `cartesian_winding` too) does the same thing indeed. It takes the side from default strategy. Wrong, it should take it from the umbrella. I thought these things were already done.  
  
I'm not in favor of redesigning strategies again - we just finished, or nearly finished, that process (umbrella). So if I understand you correctly, I should add it as a class argument and data (if any) can be passed via the constructor, instead of specifying it via `apply`. I will think about that.  
  
Anyway, thanks for your input, let's close the discussion here because it's out of this PR, you can review.

---

## Comment 21

> Username: awulkiew  
> Created_at: 2021-12-22 20:06:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-999832760  

> And use one strategy from another? This is done now.  
  
Various strategies can internally call other strategies if that's what allows us to avoid duplicating code. That's only an implementation detail. It could be replaced with formula called in both strategies, etc. This doesn't mean that these internally used strategies should be exposed in the interface of other strategies.  
  
> To be honest, I don't see why in this case this creates an inconsistency... IMO using two different side strategies (as can be done now) is inconsistent.  
  
If you define a strategy and pass it into a different strategy which was not designed around the former strategy then the result may still be wrong. Consider the spherical example I gave before. Even if you used spherical side formula (3d vectors) to calculate side everywhere (in intersection, winding, etc.) but used spherical math to calculate intersection these methods wouldn't be consistent with each other. The burden of defining consistent strategies would still rely on umbrella strategy. The proposed change wouldn't have any effect regarding this. It'd only make things more complex i.e. it'd add another parameter changing the behavior of a strategy that might be incompatible with some parts of this strategy. E.g. if we wanted to test such intersection strategy we'd have to know which side strategies it is able to take to produce correct results.  
  
> Actually I was not aware that, at this moment, still, the oriented_winding strategy (and cartesian_winding too) does the same thing indeed. It takes the side from default strategy.  
  
AFAIR the default strategy mechanism was added by you to avoid using `#ifdefs` and was a part of the experiment of replacing `side_by_triangle` with `side_robust` which AFAIU improved some cases and break other ones. This was not the original solution. Originally only one `side_by_triangle` strategy was used in the cartesian intersection strategy. There are many tweaks in intersection strategy to ensure that both of them work well with each other.  
  
> Wrong, it should take it from the umbrella. I thought these things were already done.  
  
No they were not and I think this shouldn't be done. In principle non-precise intersection strategy doesn't guarantee to work well with precise side strategy. Just like intersection strategy modeling segment as geodesics will not work well with side strategy modeling segments as loxodromes. Just like intersection strategy using spherical math will not work well with side strategy using ECEF vectors. AFAIU in principle intersection strategy doesn't have anything to do with side computation. The fact that in Boost.Geometry side strategy is used in cartesian intersection strategy is only an implementation detail.  
  
I think a strategy shouldn't have to guarantee to take any other strategy. The interfaces in general should be the least complex as possible. A strategy should only calculate CS-specific result for whatever input is passed into it.  
  
Consider the fix I proposed for non-cartesian envelope of a polygon: https://github.com/boostorg/geometry/pull/936 where winding strategy is called internally in envelope strategy to check if a pole is contained in a polygon. This winding strategy use special spherical/geographic side strategy (the same for both CSes) capable only to tell the relation WRT a pole assuming other conditions are met (e.g. there is no segment going through the pole). This makes sense only in this case and shouldn't be exposed anywhere. This is only an implementation detail. It could potentially be replaced with some other method. The fact that winding strategy with custom side strategy is used internally doesn't mean that we have to expose it in the interface.  
  
> So if I understand you correctly, I should add it as a class argument and data (if any) can be passed via the constructor, instead of specifying it via apply.  
  
I don't know if you should. You could. If cartesian intersection strategy can work correctly with various side strategies. There are so many tweaks in this strategy that my guess would rahter be that it can properly work only with one side strategy. But if you want to do this then just look at winding or distance strategies. AFAIR they were implemented like this from the beginning.

---

## Comment 22

> Username: tinko92  
> Created_at: 2021-12-28 20:42:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-1002274138  

Because I wrote some of the code used by side_robust, I would like to offer my perspective on the different side strategies, I hope the following can help with the decision. In short, I don't think a side-strategy can be at the same tolerant of near-collinearity (e.g. having an epsilon policy), simple and robust (as in not having substantial bugs for at least some inputs).  
  
The orientation predicate side_robust (with fp_equals_policy and a binary floating-point calculation type) is exact on double/float-coordinates (ignoring overflow/underflow issues for very small/large numbers) and thereby guarantees logical consistency across multiple calls, e.g. (assuming no duplicate points) if p1, p2, p3 and p1, p2, p4 are collinear, then side_robust will also see p1, p3, p4 as collinear / if p1, p2, p3 are not collinear but p1, p2, p4 are, then side_robust will not see p1, p3, p4 as collinear. So, for an algorithm like e.g. a Triangulation algorithm, which does not construct any new points and is correct under the real RAM model for its predicate, this correctness-guarantee of side_robust should guarantee valid, correct triangulations. That is the motivation for side_robust.  
  
I looked into some of the failing tests in Boost.Geometry because @barendgehrels  mentioned that side_robust causes regressions without rescaling. For example, the difference test for LINESTRING(2 8,4 0.4,8 1) minus LINESTRING(0 -0.2,8 1) failed in my test. The correct result in decimal arithmetic is MULTILINESTRING((2 8,4 0.4)). In double arithmetic, this problem cannot be expressed correctly with point_xy<double> because decimal 0.4 is the infinite binary fraction 0.01100110..._2, which is rounded at the 53rd binary digit when stored in double, similarly for -0.2. So, as @vissarion  mentioned, the inaccuracy is already there before the geometries are given to bg::difference and the side_strategy.  
  
A) For these double approximations of decimal numbers side_robust correctly says that POINT(0 -0.2)	POINT(8 1)	POINT(4 0.4) are not collinear and the difference algorithm computes MULTILINESTRING((2 8,4 0.4,8 1)) if it uses side_robust, which, I think, is correct for the nearest double-approximation of the problem.  
B) side_by_triangle with its epsilon policy will declare POINT(0 -0.2), POINT(8 1), POINT(4 0.4) to be collinear and produce the result that was perhaps expected by the user.  
C) If the problem is rescaled to integers with any factor divisible by 10, this will cause a rounding error that happens to exactly compensate the original error introduced from converting "0.4" and "-0.2" to double and probably also deliver the expected result.  
  
B) and C) will produce the expected result but they do so by making subtle errors themselves that happen to often mask or compensate the subtle degeneracies in test cases that are written in particular ways, e.g. reading decimal numbers with few significant digits from text-input, whose magnitude is not too much larger or too much smaller than 1.  
  
But programs can be written that will make the non-robust predicates behave in unexpected ways, too, e.g. consider the line strings a=LINESTRING(0.01 68.39,100 94.39) and b=LINESTRING(77.63 0.01,80.32 100). If I compute their intersection with boost::geometry (an inexact construction), I get c=POINT(80.029373406089661 89.197117797363049). side_by_triangle (compile with g++ -O3 -march=native) will return a non-zero result for a.first, a.second and c, so its epsilon tolerance can be easily exceeded with simple examples that intuitively would be collinear by construction, and side_by_triangle also returns the same value for (a.first, a.second, c), (c, a.first, a.second) and (a.first, c, a.second) which also defeats the lexicographic sorting that was probably intended to make it consistent for permutations of three points.  
  
So, in summary, I would say:  
naive computation ( det = (ax-cx)*(by-cy) - (ay-cy)*(bx-cx); return (det > 0) - (det < 0); ):  
+ potentially fastest  
- least robust  
- can easily produce counterintuitive results  
  
side_robust with fp_equals:  
+ robust (never contradicts itself, exact results for float/double-coordinates)  
+ relatively fast  
- will produce counterintuitive results for many almost-collinear points, like points from decimal inputs that were read to double  
  
side_robust with epsilon policy:  
+ relatively fast  
+ will not give the opposite of the correct sign (it will not say left when it is right, but it might say collinear)  
+ probably produces fewer counterintuitive results for decimal inputs  
- not robust (can contradict itself)  
  
side_by_triangle with epsilon policy  
+ probably produces fewer counterintuitive results for decimal inputs  
- not robust (can contradict itself, depending on compilation options, even for permutations of three points)  
- lots of unpredictable branches for the implicit lexicographic sorting (performance penalty)  
  
I don't think there exists a solution that is simultaneously fast, simple and produces neither counterintuitive results nor invalid results for at least some cases (non-robustness). A solution that is simultaneously robust and produces expected results for decimal inputs and constructed points, would have to read the WKT as decimal, convert it to an arbitrary precision rational type and perform all constructions and predicate calls with that. I tested that and it produces MULTILINESTRING((2 8,4 2/5)) for the example problem, as expected.   
  
I think for exact, arbitrary precision number types (I think I saw tests for rational), the naive computation without epsilon policy is always a good choice. And I think for integers (including rescaled coordinates) that fit into the mantissa of doubles (<2^53), converting to double and using side_robust with fp_equals policy is always a good choice because it will guarantee a correct result in terms of the integer input and avoid the potential integer overflow for integers that are larger than the square-root of the largest allowed integer value.  
  
In case, it could be a useful solution, I implemented a (branchless) side-strategy that returns collinear if the orientation is unclear either due to rounding-error in the computation or due to rounding error in the input coordinates, see here: https://gist.github.com/tinko92/5f242339ae312f66206f2be69184be72 .

---

## Comment 23

> Username: barendgehrels  
> Created_at: 2021-12-29 09:22:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-1002478142  

Thanks for all input. I'll come back to it.  
  
But we're talking now about things not in this PR. I'm curious if I can merge it.

---

## Comment 24

> Username: barendgehrels  
> Created_at: 2021-12-29 12:01:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-1002561026  

> In case, it could be a useful solution, I implemented a (branchless) side-strategy that returns collinear if the orientation is unclear either due to rounding-error in the computation or due to rounding error in the input coordinates, see here: https://gist.github.com/tinko92/5f242339ae312f66206f2be69184be72 .  
  
@tinko92 I tested that one and it looks promising! A quick run shows that almost all tests succeed, and the few tests which don't succeed are usually the really fragile tests. Plus a few in buffer to be investigated further (but these aren't trivial either). So it that sense it's much more compatible to the current implementation. This unlike the robust version which let some trivial testcases fail (for double coordinates, no rescaling).  
  
The implementation is concise, are the `5` and `56` a kind of tunable constants or what is the reasoning behind them?

---

## Review 25 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-29 13:13:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/952#pullrequestreview-841397425  

📁 test/algorithms/set_operations/intersection/alternative_side_strategy.hpp

```diff
  15 |+ 
  16 |+ template <typename SideStrategy>
  17 |+ class alternate_side_strategy
```

> Username: awulkiew  
> Created_at: 2021-12-29 13:13:38 UTC  
> Updated_at: 2021-12-29 13:37:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r776319987  

Technically this is not side strategy but umbrella strategy using non-standard side strategy. But since this is only for testing it's ok.


---

## Review 26 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-29 13:33:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/952#pullrequestreview-841408230  

📁 test/algorithms/set_operations/intersection/alternative_side_strategy.hpp

```diff
  37 |+     {
  38 |+         return default_umbrella::relate();
  39 |+     }
```

> Username: awulkiew  
> Created_at: 2021-12-29 13:33:25 UTC  
> Updated_at: 2021-12-29 13:40:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#discussion_r776328186  

This is in the tests so ok but there are some technicall issues I'd like to point out in case you wanted to build on top of it in the future.  
  
1. If these functions are needed then this strategy should have been derived from `bg::strategies::relate::cartesian<>`. Then you could ommit these functions. Later if you needed to overload one of them you could bring the rest to the scope with e.g. `using bg::strategies::relate::cartesian<>::relate`.  
  
2. `SideStrategy` may be incompatible with intersection and winding strategies. I'd implement base strategies for them taking `SideStrategy` as template argument and derive the public versions from these bases to hide this additional `SideStrategy` argument. Like it was done for spherical winding here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/spherical/point_in_poly_winding.hpp#L51-L52  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/spherical/point_in_poly_winding.hpp#L499-L511


---

## Comment 27

> Username: awulkiew  
> Created_at: 2021-12-29 13:37:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-1002598347  

@barendgehrels Yes, I'm ok with merging. Thanks for the discussion!  
  
I commented about some issues that might be a problem in future PRs but they don't prevent this one to be merged.

---

## Comment 28

> Username: tinko92  
> Created_at: 2021-12-29 13:52:06 UTC  
> Updated_at: 2021-12-29 14:11:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-1002604497  

> The implementation is concise, are the `5` and `56` a kind of tunable constants or what is the reasoning behind them?  
  
@barendgehrels They are the result of numerical forward error analysis but the 56 could be 32 (I think made a mistake yesterday, I corrected the gist. It shouldn't matter much, though. the impact of the coefficient for epsilon² is almost negligible).   
  
## Reasoning  
They are computed by starting with an error of |x| \* eps for each input and iteratively computing and simplifying error bounds for each +,-,\*-operation. The final error bound is (5*eps + 25*eps²) and the least upper bound in floating-point numbers for that is (5*eps + 32*eps²). Edit: [sketch proof.pdf](https://github.com/boostorg/geometry/files/7789179/sketch.proof.pdf)  
If the determinant is larger than this bound then it should be guaranteed to be correct, otherwise, it's uncertain due to either rounded inputs or rounding errors in the computation of the determinant.  
  
## Tunability  
5 and 32 should be the smallest (or very close to the smallest) coefficients if one wants to account for rounded inputs and computation error in the side computation. 3 and 16 would only account for the computation error in the side computation and assume precise inputs.  
One could also increase the epsilon to account for even worse rounding in the inputs rather than rounding to the nearest float E.g. set epsilon to 5*10^-(x+1) to only consider inputs precise up to the x-th digit rather than to machine epsilon, e.g. to consider something collinear, if it was collinear in some program, written out with to_wkt with limited digits and read back. But at that point the bound would be too pessimistic, IMHO, and it would be to the detriment of someone who purposefully enters more than x digits into points.  
I don't think it can be tuned to be consistent with all constructions because one cannot really compute a rigorous error bound/epsilon tolerance for a point that was computed e.g. as the intersection of two segments without knowing the original coordinates of the two segments.

---

## Comment 29

> Username: barendgehrels  
> Created_at: 2021-12-30 10:03:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-1002957062  

> @barendgehrels Yes, I'm ok with merging. Thanks for the discussion!  
>   
> I commented about some issues that might be a problem in future PRs but they don't prevent this one to be merged.  
  
Thanks @awulkiew , I applied the renaming and inheritance and I'll merge it.

---

## Comment 30

> Username: barendgehrels  
> Created_at: 2021-12-30 10:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-1002961759  

> 5 and 32 should be the smallest (or very close to the smallest) coefficients if one wants to account for rounded inputs and computation error in the side computation.   
@tinko92 thanks again, great  
  
Indeed the behavior looks the same with 5/56  
  
Next week (=year) I'll look further and probably create a PR with this new strategy.

---

## Comment 31

> Username: barendgehrels  
> Created_at: 2021-12-30 10:32:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/952#issuecomment-1002967839  

> They are computed by starting with an error of |x| * eps for each input and iteratively computing and simplifying error bounds for each +,-,*-operation. The final error bound is (5_eps + 25_eps²) and the least upper bound in floating-point numbers for that is (5_eps + 32_eps²). Edit: [sketch proof.pdf](https://github.com/boostorg/geometry/files/7789179/sketch.proof.pdf) If the determinant is larger than this bound then it should be guaranteed to be correct, otherwise, it's uncertain due to either rounded inputs or rounding errors in the computation of the determinant.  
  
Great explanations and document! Thanks!  
  
It's merged now.  
@awulkiew @vissarion @mloskot @tinko92 Happy New Year!

---
