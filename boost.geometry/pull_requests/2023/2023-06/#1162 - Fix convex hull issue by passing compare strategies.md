# #1162 Fix convex hull issue by passing compare strategies [Merged]

> Username: vissarion  
> Created at: 2023-06-16 13:57:14 UTC  
> Updated at: 2023-09-27 09:19:52 UTC  
> Merged at: 2023-07-28 08:38:22 UTC  
> Closed at: 2023-07-28 08:38:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162  

This PR propose a fix for https://github.com/boostorg/geometry/issues/1158  
  
First, it enables passing (umbrella) strategies to less policy. Then different (umbrella) strategies can parameterize the less policy by different compare strategy.   
  
For, example floating point comparison in convex hull strategies does *not* need to be done approximately. Relate strategies still use the approximate comparison of fp numbers (`math::equals()` function).  
  
Side note: I am not sure if approximate comparison of numbers is needed for relate strategy. I just left it as is in order for the tests to pass, but I think it needs some investigation. Related issue https://github.com/boostorg/geometry/issues/1148   
  
Next steps (to be implemented in new PRs)  
- do the same for `greater` and `equal_to`  
- move code from `strategies/compare.hpp` to `strategies/cartesian/compare.hpp` (if I understand the strategies code structure correctly, @awulkiew please correct me if I am wrong here)

---

## Review 1 [Changes requested]

> Username: barendgehrels  
> Created_at: 2023-07-02 08:50:23 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/1162#pullrequestreview-1509505632  

As mentioned in my comments, this looks like breaking...  
  
Can it be solved in a different way too? A way does not change `less` itself?

📁 include/boost/geometry/algorithms/detail/convex_hull/graham_andrew.hpp

```diff
 225 |             SideStrategy const& side)
 226 |     {
 227 |+         //std::cout << "add to hull" << std::endl;
```

> Username: barendgehrels  
> Created_at: 2023-07-02 08:41:04 UTC  
> Updated_at: 2023-07-02 08:50:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1249357912  

Is this a left over?

> Username: vissarion  
> Created_at: 2023-07-05 12:21:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1253026543  

Yes, will be removed (together with a few other comments).


📁 include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp

```diff
  67 |     class unary_not_disjoint_predicate
  68 |-         : geometry::less<void, -1, CSTag>
  68 |+         : geometry::less<void, -1, Strategy>
```

> Username: barendgehrels  
> Created_at: 2023-07-02 08:45:36 UTC  
> Updated_at: 2023-07-02 08:50:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1249360115  

Can we call it maybe: `CompareStrategy` ?

> Username: vissarion  
> Created_at: 2023-07-05 12:22:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1253027838  

I do not know, I kept it that way since this is the name used by apply function (below) that actually pass that type.

> Username: barendgehrels  
> Created_at: 2023-07-07 12:01:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1255717737  

ok

---

📁 include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp

```diff
  70 |     private:
  71 |-         typedef geometry::less<void, -1, CSTag> base_type;
  71 |+         //TODO: pass strategy
```

> Username: barendgehrels  
> Created_at: 2023-07-02 08:46:12 UTC  
> Updated_at: 2023-07-02 08:50:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1249360384  

Is this still TODO or already done? Because it is passed now. Or do you want to pass an instance? If so, can you rephrase the comment?

> Username: vissarion  
> Created_at: 2023-07-05 12:22:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1253028367  

This is done, removed now.

---

📁 include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp

```diff
 111 |                 typename std::vector<point1_type>::const_iterator,
 111 |-                 cs_tag
 112 |+                 Strategy
```

> Username: barendgehrels  
> Created_at: 2023-07-02 08:47:08 UTC  
> Updated_at: 2023-07-02 08:50:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1249360823  

This (for example) looks like a breaking change. Can you point if there is something which could (potentially) cause non compilations for existing users?

> Username: vissarion  
> Created_at: 2023-07-05 12:27:29 UTC  
> Updated_at: 2023-07-05 12:27:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1253034146  

All strategies (apart from convex hull strategy) calls the old code at the end so AFAIU there is no issue at this point. But there are breaking changes in this PR (see below).


📁 include/boost/geometry/algorithms/detail/relate/point_point.hpp

```diff
 226 |-         typedef geometry::less<void, -1, CSTag> less_type;
 225 |+         //TODO: pass strategy
 226 |+         typedef geometry::less<void, -1, Strategy> less_type;
```

> Username: barendgehrels  
> Created_at: 2023-07-02 08:49:35 UTC  
> Updated_at: 2023-07-02 08:50:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1249362143  

I'm a bit worried that `geometry::less` is changed. This really looks like a breaking change to me...  
  
So it fixes convex hull (good!) but it needs also changes in many other operations like `relate` and `is_valid` etc...

> Username: vissarion  
> Created_at: 2023-07-05 12:34:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1253042333  

For everything apart from convex hull the same strategy is called. E.g. for cartesian it is `cartesian<ComparePolicy, EqualsPolicy, -1>` which is as the old one https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/compare.hpp#L165 (also called from default compare strategy) with the only difference that now it has the EqualsPolicy parameter which for all algorithms is as before (i.e. `strategy::compare::equals_epsilon` see `strategies/relate/cartesian.hpp` but for convex hull it is `strategy::compare::equals_exact` see `strategies/convex_hull/cartesian.hpp`). This change eventually change only convex hull strategies.

> Username: barendgehrels  
> Created_at: 2023-07-07 07:15:13 UTC  
> Updated_at: 2023-07-07 07:15:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1255360300  

But `less` it is a public policy, meant for all library users. It’s not internal. So that we don’t use it like this does not mean that we can change it.

> Username: vissarion  
> Created_at: 2023-07-07 07:27:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1255374045  

This is updated and now the user can pass either a strategy or a cs tag as the third parameter to `less` (the second choice can be considered as deprecated and removed in the future). So in some sense now `less` is "enhanced". What do you think?

> Username: barendgehrels  
> Created_at: 2023-07-07 12:02:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1255718344  

sound good!


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2023-07-02 08:54:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1162#pullrequestreview-1509507954  

📁 include/boost/geometry/policies/compare.hpp

```diff
  44 |     int Dimension = -1,
  44 |-     typename CSTag = void
  45 |+     typename Strategy = void
```

> Username: barendgehrels  
> Created_at: 2023-07-02 08:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1249364097  

In summary, this is the main thing I'm worried about.

> Username: vissarion  
> Created_at: 2023-07-05 12:34:32 UTC  
> Updated_at: 2023-07-05 12:34:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1253042578  

See above.


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2023-07-02 08:54:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1616474403  

Copying your description, maybe just:  
* `less_with_compare_strategy`   
* `greater_with_compare_strategy`  
* etc  
Though these names are a bit long.  
  
Alternatively the `less` could be meta-templated such that it accepts either `CSTag` or a `CompareStrategy`.

---

## Comment 4

> Username: vissarion  
> Created_at: 2023-07-05 12:37:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1621672423  

First, many thanks for the review!  
  
> Copying your description, maybe just:  
>   
>     * `less_with_compare_strategy`  
>   
>     * `greater_with_compare_strategy`  
>   
>     * etc  
>       Though these names are a bit long.  
>   
>   
> Alternatively the `less` could be meta-templated such that it accepts either `CSTag` or a `CompareStrategy`.  
  
The breaking change I see is in the case some user define a less operator with a CSTag template parameter. I added specializations to support this case too. Please let me know what you think after these changes.   
  
Side comment, I think it is more correct (according to the library design) to pass a strategy to less than a cs_tag.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2023-07-05 18:09:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1622240363  

Strategies are for CS-specific computation. AFAIU you want to compare coordinates strictly instead with epsilon in an algorithm. So the differentiation is made based on algorithm, not CS. This probably should not be a strategy but a policy (custom less policy) used only in convex_hull.  
  
Consider this. The most general umbrella strategy can be passed to all algorithms, including `relate` and `convex_hull`. Which compare strategy should be returned by it?

---

## Comment 6

> Username: awulkiew  
> Created_at: 2023-07-06 00:11:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1622715535  

What do you think about defining less compare policy in convex_hull strategy?

---

## Comment 7

> Username: vissarion  
> Created_at: 2023-07-06 13:47:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1623713847  

> Strategies are for CS-specific computation. AFAIU you want to compare coordinates strictly instead with epsilon in an algorithm. So the differentiation is made based on algorithm, not CS. This probably should not be a strategy but a policy (custom less policy) used only in convex_hull.  
>   
> Consider this. The most general umbrella strategy can be passed to all algorithms, including `relate` and `convex_hull`. Which compare strategy should be returned by it?  
  
@awulkiew that's a good point!  
  
In https://github.com/boostorg/geometry/pull/1162/commits/f719a26b442eb12c0f91fba5cbb980758164cd77 I defined a new less policy (to be used only by convex hull for now) that passes the right equals policy to the strategy. Thus the strategy is responsible for the CS specific computation and the less policy (in the algorithm level) is responsible for the comparison of coordinates (exact or with epsilon). Alternatively, I could have added another template to less (i.e. EqualsPolicy).   
  
@awulkiew what do you think? I think it is better this way that defining less inside convex hull strategy, this way strategy does not have any information on how we compare coordinates but this is passed by the algorithm. Now type alias `compare` is the same in both `relate` and `convex hull` strategies.  
  
BTW a similar problem holds with `side` method on those strategies, where `relate` uses a default side strategy while `convex hull` a robust one, this is a non-CS specific differentiation.

---

## Review 8 [Approved]

> Username: barendgehrels  
> Created_at: 2023-07-07 12:08:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1162#pullrequestreview-1518687714  

thanks!

📁 include/boost/geometry/policies/compare.hpp

```diff
 126 |+ template <typename Point, int Dimension>
 127 |+ struct less<Point, Dimension, boost::geometry::cartesian_tag>
 128 |+ {
```

> Username: barendgehrels  
> Created_at: 2023-07-07 12:08:26 UTC  
> Updated_at: 2023-07-07 12:08:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#discussion_r1255725501  

Ok, I see. Indeed `less` is enhanced now, and it wouldn’t break, and these are the implementations.   
So I’m ok now


---

## Comment 9

> Username: awulkiew  
> Created_at: 2023-07-21 21:43:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1646266894  

Thanks, it looks better now. But I have one more concern. What if someone passes a different, non-robust or maybe non-cartesian side strategy (assuming it works) into the convex_hull? Would less_exact be the correct choice compatible with strategies other than robust side? Or am I missing something about the way how less compare is defined?

---

## Comment 10

> Username: vissarion  
> Created_at: 2023-07-24 12:39:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1647835578  

So here the less strategy does have to do with robust side strategy. It is related to how we sort inside convex hull. If you sort with approximate less then you will run into issues like the one this PR solves when e.g. you have input points with very similar x or y coordinates. And these issues will be independent from side strategy.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2023-07-26 22:36:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1652629671  

Right, thanks for the explanation.  
Since `less_exact` is explicitly only used in `convex_hull` does compare strategy has to be defined in umbrella strategies? AFAIU the only change that is needed is to use specific `less` in the algorithm. Or am I missing something?

---

## Comment 12

> Username: vissarion  
> Created_at: 2023-07-27 13:35:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1653642918  

You mean to pass just the (non umbrella) compare strategy to `less_exact` or even define `less_exact` inside `convex_hull`? I implemented that way because I need to change how numbers are compared inside  compare strategies (e.g. https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/compare.hpp#L93). Also, `less` and `less_exact` policies now take a strategy instead of a CStag which I think is more correct, right?  
  
Moreover, even not tested, I think that there could be similar problems in the library where `std::sort` is used to sort points or turns. So, it is eventually not only convex hull to be affected.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2023-07-27 20:57:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1654567885  

Yes, I meant having `less_exact` policy alongside `less` policy, both taking CSTag and using `less_exact` in `convex_hull`. The rationale behind this was to introduce least amount of changes.  
  
> take a strategy instead of a CStag which I think is more correct  
  
To be honest I don't know which is more correct. I feel that the whole less policy could itself be a strategy because it's almost wholly CS-specific. But taking the strategy seems to be more "correct" than taking CSTag.

---

## Comment 14

> Username: andrea-cimatoribus-pix4d  
> Created_at: 2023-09-27 09:19:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1162#issuecomment-1737026794  

I missed the notification from this, but thanks a lot @vissarion for the PR.

---
