# #381 Strategies and variant support in set operations, is_valid and is_simple. [Merged]

> Username: awulkiew  
> Created at: 2017-02-17 04:51:55 UTC  
> Updated at: 2017-09-06 12:27:41 UTC  
> Merged at: 2017-02-27 13:35:16 UTC  
> Closed at: 2017-02-27 13:35:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/381  

This PR also adds area and Pt/Pt distance strategies getters to cartesian and spherical intersection strategies.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-02-18 15:09:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-280851337  

More and more strategies getters are added into the intersection strategy, there are also getters in winding strategy. This may be not the end of adding strategies there becasue the envelope strategy and maybe disjoint segment/box strategies getters would be needed as well. So maybe we should think about more general approach? We could at least move the getters from strategies to somewhere outside, because the internals of a strategy are not the right place for this mechanism. I'm considering moving them into the `bg::strategy::xxx::services` namespace. It could look like that:  
  
    typedef bg::strategy::intersection::relate_spherical_segments<> strategy_type;  
    strategy_type strategy;  
  
    // ...  
  
    typename bg::strategy::area::services::rebind_type<strategy_type, Geometry>::type  
        area_strategy = bg::strategy::area::services::rebind<Geometry>(strategy);  
  
What do you think?

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2017-02-20 19:46:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/381#pullrequestreview-22828199  

📁 include/boost/geometry/strategies/cartesian/cart_intersect.hpp

```diff
 129 |+         typedef typename distance_strategy<Geometry>::type strategy_type;
 130 |+         return strategy_type();
 131 |+     }
```

> Username: barendgehrels  
> Created_at: 2017-02-20 19:46:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#discussion_r102082911  

Are these two strategies (area, distance) used in the intersection strategy? Don't see that. I'm doubting if it really belongs here - we should avoid that intersect becomes the umbrella strategy... Or do I miss something?

> Username: awulkiew  
> Created_at: 2017-02-20 20:04:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#discussion_r102085193  

It's not used in the strategy however an algorithm which use the intersection strategy also needs area strategy. The algorithm is equals() which does this TrivialCheck comparing areas of polygons first before calculating turns.


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2017-02-20 19:51:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-281167677  

Thanks for the PR! Lot of work. I did ask a question but now that I re-read your comment, we agree ;-)   
So yes, let's not add them all to intersection.   
But I'm not sure that I understand your suggestion. The intention is that users can pass strategies to functions. Is that possible with your rebinding? Does it get a belonging strategy from another?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2017-02-20 20:18:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-281172720  

The intention is to allow the user to pass one strategy, this strategy being the most "high-level" strategy that is needed and then derive all other strategies from it internally in the algorithm. And to require the same type of strategy across all similar algorithms, e.g. in all relation and set operations for Areal and Linear geometries require Segment/Segment intersection strategy.  
  
The alternative is to force the user to pass an umbrella strategy but then he/she'd be forced to know which combinations of various strategies are compatible with each other and which are needed by which algorithm. E.g. in order to call equals(Linear, Linear), disjoint(Linear,Linear) and within(Linear, Linear) he'd be forced to pass 3 different umbrella strategies one containing intersection-Seg/Seg and distance-Pt/Pt strategies (equals), one intersection-Seg/Seg, envelope-Seg and disjoint-Seg/Box strategies (disjoint) and one intersection-Seg/Seg, side and envelope-Seg strategies (within).  
  
So this mechanism I mentioned would allow to derive "lower-level" strategies (from the algorithm or interface point of view) from "higher-level" strategy. Not any strategy from any strategy (at least for now), only the ones which are needed in algorithms.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2017-02-20 21:01:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-281180324  

OK, that makes it more clear. So we need a hierarchy of strategies (because otherwise 'higher' level is not defined). If one strategy can be derived (we need another word there) from another, the strategies can become all independent (which is good).  
  
However, I think that this is maybe not enough. Now the 'lower level' strategies such as **distance** (at least distance point-point) can be passed to an algorithm, or **within**, or **area**, all of which we have different versions for different purposes. If we automatically gather strategies, and it automatically retrieves the within- , area- and distance-strategies from e.g. intersection (or another one), the user cannot explicitly select them. Which was the intention (and also that intention of the umbrella strategy...).  
  
BTW, this can become a long discussion and need research etc. I'm not against merging, we can sort this out after the merge and move those strategies from intersect to somewhere else later.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2017-02-21 01:22:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-281218983  

> (we need another word there)  
  
Yes, I was thinking about `rebind` taken from Allocator though now when I think about it there is a conceptual difference. In the case of Allocator it is rebound for a different value type, so it's the same allocator but working with a different type. In the case of strategy this would be reversed, so it'd return a different strategy for the same parameters, i.e. coordinate system, formulas and approximation orders. So maybe `rebind` is not a good name. Maybe something "obvious" like `services::from_other` or `services::from_strategy`?  
  
> However, I think that this is maybe not enough (...)  
  
This mechanic could be used for umbrella strategies as well, the same function/trait could be used. This way the user could pass either an umbrella strategy if we implemented it at some point or one, high-level strategy. However I'd wait with implementing umbrella strategies unless a user explicitly asks about it. It's because I doubt the users would like to specify the behavior of algorithms at that level of complexity and also because it wouldn't make sense to use e.g. different spheroid models or formulas in different strategies used internally by the algorithm. Until now it was impossible to pass strategies to most of the relops and all setops and AFAIK noone requested any change regarding it.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2017-02-21 20:43:38 UTC  
> Updated_at: 2017-02-21 20:44:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-281474083  

infer? deduce?  
  
OK, if the approach can be used for umbrella strategies too, we don't exclude that for the future so that is good. OK, nobody asked for it indeed...   
On the other hand - if we derive or rebind or infer everything from one thing passed, instead, the user could also just pass the spheriod model... ? What would be the difference?  
Behind the screens, or course there is a difference in behaviour (it is one of the pillars of the design), and we really need strategies internally. It does not matter how much arguments are passed.  
But I mean: for the user. Passing a (spheroid) model would maybe more obvious for them (for cartesian this would then probably be empty)

---

## Comment 8

> Username: awulkiew  
> Created_at: 2017-02-21 21:49:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-281492562  

I agree that parameters would be more convenient for the users. Still it should be possble to pass strategies because this is how the interface looks like right now. The possibility of passing strategies is a starting point. In addition to this we could support parameters but not instead.  
  
Regarding the parameters themselves, it'd not only be the spheroid model though. The user should also be able to pass parameters of formulas or formulas themselves (so anything that may be typically a template parameter of any strategy/formula used internally). And these parameters would have to allow to create all needed strategies consistently. So either the user would have to pass all required parameters for all strategies used internally or there should be a way of "deriving" parameters from other parameters. So we'd have similar problem to the strategies "deriving" problem. For example, besides the spheroid model the geodesic segments intersection strategy uses sjoberg intersection formula(s) which uses inverse formula like andoyer or vincenty and also takes the order of approximation as parameter. So if a user passed only the spheroid model and inverse formula parameter we'd have to figure out the order of approximation for sjoberg formula compatible with the inverse formula.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2017-02-22 15:19:15 UTC  
> Updated_at: 2017-02-22 15:20:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-281699243  

> infer? deduce?  
  
I'd prefer something more familiar but I don't have any good idea. E.g. `cast`, `cast_strategy`, but technically this wouldn't be casting because casting requires to express explicitly the casted type. `convert` and `create` has conceptually the same problem. We also should have in mind that this would probably be in namespace `bg::strategy::xxx::services` and that there is already `default_strategy` there (hence my proposal `from_strategy`). So `infer_strategy` or `deduce_strategy` could also work. Type *deduction* already means something else in C++ so this leaves `infer_strategy` but since I'm not a native english speaker I'm not sure if that word's usage is correct in this context. Still, we could change it in the future if needed. Or do you have some other ideas?

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2017-02-26 21:40:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-282589999  

Infer = derive by reasoning. I don't know if there is here really reasoning involved. I'm OK with from_strategy too.   
I'm still not completely sure about how it would look like. Suppose you want to use andoyer (rest default). Just pass the andoyer to intersection and there rest is 'deduced'? Same for a specific implementation of 'within' ? What if you want to specify both (OK, that is already handled - nobody did ask for this - however, it was still originally designed so it should not be blocked).

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2017-02-26 21:40:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-282590019  

Was this PR already merged?

---

## Comment 12

> Username: awulkiew  
> Created_at: 2017-02-27 00:42:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-282602989  

> I'm still not completely sure about how it would look like.  
  
Similar to how within/covered_by worked (so nothing changed here). For P/Box or Box/Box the user was required to pass P/Box or Box/Box strategies. For P/A and P/L the user was forced to pass winding strategy (now it's possible to specify side strategy in winding strategy and geographic side strategy takes inverse formula and spheroid). The new thing is added for A/A, L/A or L/L the user is forced to pass segments intersection strategy (in geographic it takes inverse formula, order of approximation and spheroid). And this model was used in all relops and setops.  
  
From the user's perspective it'd look like this:  
  
    auto strategy = bg::relate_geodesic_segments<>(spheroid);  
    bg::within(poly1, poly2, strategy);  
  
and internally it'd be something like (e.g. to check if point on border of some ring is inside other geometry):  
  
    // G1 and G2 are needed to create winding strategy  
    bg::within(pt, poly2, bg::strategy::point_in_geometry::services::from_strategy<G1, G2>(strategy));  
  
So I was thinking about 1-directional mechanism (high-to-low level), i.e. intersection-strategy could be used to get within/winding-strategy not the other way around. But the same design could be used to allow to derive any kind of strategy from any other strategy. It'd only depend on the number of partial specializations of `from_strategy`. Minimally we'd need `bg::strategy::point_in_geometry::services::from_strategy` specialized for all segment intersection strategies but we could also add the reversed case so `bg::strategy::intersection::services::from_strategy` specialized for all within strategies or distance strategies, etc. However the reverse case might be problematic because if we wanted to do it into this direction we'd have to figure out some of the parameters, in this case the order of approximation used in segment intersection strategy. This is why going from high- to low-level is more straightforward, we have needed parameters.  
  
> Was this PR already merged?  
  
No, it wasn't.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2017-02-27 07:40:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-282648595  

OK - thanks for explaining again. I'm OK with merging current state (thought it was but I later realized those are similar changes into set operations).

---

## Comment 14

> Username: awulkiew  
> Created_at: 2017-02-27 13:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/381#issuecomment-282714463  

Yes, this https://github.com/boostorg/geometry/pull/378 was similar. Ok, I'll merge it as it is and later prepare a PR moving the getters to the outside so with the actual code it'll be clear how it works and we'll be able to decide if this is the way to go. Thanks.

---
