# #812 Umbrella strategies for distance() and comparable_distance(). [Merged]

> Username: awulkiew  
> Created at: 2021-02-17 01:20:35 UTC  
> Updated at: 2021-03-31 22:08:57 UTC  
> Merged at: 2021-03-31 22:08:57 UTC  
> Closed at: 2021-03-31 22:08:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/812  

This PR is a followup of https://github.com/boostorg/geometry/pull/768  
  
The class derivation sequence is changed, i.e. `index` strategies are derived from newly added `distance` strategies and they are derived from `relate` strategies.  
  
Existing `comparable_distance()` were replaced with `distance()` getters because they are redundant. The caller can get distance strategy and convert it to comparable strategy if needed. This is what is done e.g. in `comparable_distance()` algorithm.  
  
In some cases it is required to force the algorithms to use comparable strategies internally. For this purpose I've added `strategies::distance::detail::comparable<>` wrapper returning comparable strategies from `distance()` getters. It's caused by the fact that in some cases `dispatch::distance` is dispatched/called internally and comparable strategies are passed into it for optimization.  
  
There are still some things that need addressing, basically the legacy code in some places needs to be changed (see TODO comments) but I'd prefer to keep this PR small because it's easier to review.  
  
These strategies should also be used directly in `length()`, `perimeter()`, `discrete_frechet_distance()` and `discrete_hausdorff_distance()` but I'd leave this for the next PR as well.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2021-02-17 01:42:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#issuecomment-780236383  

There is also the question about backward compatibility. This code is backward compatibile in a way that if a user passes a legacy strategy this strategy is converted to corresponding umbrella strategy and this one will be used internally. It does not matter which legacy strategy is passed. The umbrella strategy defines which legacy strategy is used for specific input geometries.  
  
For other algorithms this is not a problem because all other algorithms requires a specific strategy for specific input geometries. `distance()` is different in two ways:  
- In some cases it allows to pass multiple kinds of strategies for the same pair of geometries. E.g. it is possible to pass both Point-Segment or Point-Box strategies for Point-Box distance which results in choosing a different algorithm at compile time. And there are many combinations like that.  
- In other cases a different strategy can be created based on passed strategy and coordinate systems of input geometries. For that see `detail/distance/backward_compatibility.hpp` file containing the code creating Point-Segment strategy based on Point-Point strategy for calculating distance between PointLike and Linear/Areal geometries.  
  
So my question is. How backward compatible do we want to be? Should we be fully backward compatible ensuring that exactly the same legacy strategies are used internally in the algorithms when a user passes them? Or would compilation of the old code and the same result (or potentially very close) even though calculated differently be enough? Maybe besides a case when a user passed incorrect strategy in which case the result would be different but correct.

---

## Comment 2

> Username: vissarion  
> Created_at: 2021-03-02 09:34:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#issuecomment-788767087  

>So my question is. How backward compatible do we want to be? Should we be fully backward compatible ensuring that exactly the same legacy strategies are used internally in the algorithms when a user passes them? Or would compilation of the old code and the same result (or potentially very close) even though calculated differently be enough? Maybe besides a case when a user passed incorrect strategy in which case the result would be different but correct.  
  
I do not mind keeping full backward compatibility as long as the result is correct.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2021-03-02 14:36:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/812#pullrequestreview-601624624  

Thanks for this PR!

📁 include/boost/geometry/algorithms/detail/distance/linear_or_areal_to_areal.hpp

```diff
  72 |         >::type return_type;
  73 | 
  74 |     static inline return_type apply(Areal1 const& areal1,
```

> Username: vissarion  
> Created_at: 2021-03-02 09:48:46 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r585416856  

what about returning `auto` here?

> Username: awulkiew  
> Created_at: 2021-03-16 20:09:40 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r595505454  

The rules for C++14 `auto` type deduction requires that in all places the same type is returned. Here in one place it's the return type of `linear_to_linear<...>::apply(...)` and in the other place it's `int` (`0`). I could use `?:` operator instead of `if` but then we'd be dealing with the rules defining the type of this expression. In particular with the rule saying that if both return types are convertible to each other then the program is ill-formed. So e.g. this would not compile for a user-defined numeric type constructible from and convertible to `int`.  
  
In short, I'd still have to define some return type inside this function and convert `0` to it before returning it. And I think it's not worth it.  
  
What I could do instead would be using `bg::distance_result<Areal1, Areal2, Strategies>::type` or implement a simple template alias to avoid using heavier `distance_result`:  
```  
template <typename G1, typename G2, typename Strategies>  
using distance_return_t = typename strategy::distance::services::return_type  
    <  
        decltype(std::declval<Strategies>().distance(std::declval<G1>(), std::declval<G2>())),  
        typename point_type<G1>::type,  
        typename point_type<G2>::type  
    >::type;  
```  
then the lines above would look like this:  
```  
typedef distance_return_t<Areal1, Areal2, Strategies> return_type;  
```  
Unless you have a different idea?

> Username: vissarion  
> Created_at: 2021-03-17 08:32:09 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r595805867  

>In short, I'd still have to define some return type inside this function and convert 0 to it before returning it. And I think it's not worth it.  
  
Agree, thanks for the explanations.   
  
> Unless you have a different idea?  
  
The alias make sense since this is used (as far as I remember) in a few places in distance implementation. But I am also OK leaving it as is, since the use of `auto` is problematic.

> Username: awulkiew  
> Created_at: 2021-03-25 02:19:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r600995498  

I refactored the return types and some other typedefs. For this purpose I added several template aliases like the one above. Please see if you're ok with the changes.


📁 include/boost/geometry/algorithms/detail/distance/linear_to_box.hpp

```diff
  39 |     static inline return_type apply(Box const& box,
```

> Username: vissarion  
> Created_at: 2021-03-02 09:50:09 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r585417988  

if return `auto` here then `strategy_type` is not needed to be defined ?

> Username: awulkiew  
> Created_at: 2021-03-16 20:12:14 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r595506994  

In this case yes, because there is one return statement. But not in the member function below also returning `0`. I described the reason in the conversation above.


📁 include/boost/geometry/algorithms/dispatch/distance.hpp

```diff
  53 |+ };
  54 |+ 
  55 |+ // TODO: right now legacy single strategy can be passed here in some cases
```

> Username: vissarion  
> Created_at: 2021-03-02 09:52:42 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r585419899  

this comment refers probably to the dispatch above

> Username: awulkiew  
> Created_at: 2021-03-16 20:16:32 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r595509552  

Actually no, it refers to this particular specialization. `distance_strategy_type` is dispatched not only by `IsUmbrella` but also by `Reverse`. So if we decided to not support legacy strategies then we could remove this specialization but the one reversing the arguments would stay.

> Username: vissarion  
> Created_at: 2021-03-17 08:33:17 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r595806555  

OK, thanks.


📁 include/boost/geometry/strategies/distance/comparable.hpp

```diff
  28 |+ template <typename Strategies>
  29 |+ struct comparable
  30 |+     : public Strategies
```

> Username: vissarion  
> Created_at: 2021-03-02 09:57:32 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r585423442  

indentation looks weird here

> Username: awulkiew  
> Created_at: 2021-03-16 20:19:37 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r595511680  

What do you mean? Aren't we indent base classes list like that? Is it because in this case there really no need for that so the base class could be at the same line as the derived class? Or do you mean something else?

> Username: vissarion  
> Created_at: 2021-03-17 08:44:30 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r595814465  

I was thinking that there is no need in this case, as for example here   
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/area/cartesian.hpp#L27  
but now I am thinking it again I will agree with you to be consistent and always change line.

> Username: awulkiew  
> Created_at: 2021-03-25 02:10:16 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r600992752  

Ok, I plan to alter the strategies in other PRs so I'll make the cartesian area strategy consistent.


---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-03-19 19:03:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#issuecomment-803048440  

I implemented a backward compatibility mechanism for custom Pt/Pt strategies which creates umbrella strategies from CS of geometries, so it works the same way the old backward compatibility mechanism worked.

---

## Review 5 [Approved]

> Username: barendgehrels  
> Created_at: 2021-03-24 13:05:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/812#pullrequestreview-619686368  

I didn't review all lines, but the umbrella strategy in general is very welcome

📁 include/boost/geometry/algorithms/detail/distance/linear_or_areal_to_areal.hpp

```diff
  34 |         <
  35 |-             Strategy,
  35 |+             decltype(std::declval<Strategies>().distance(std::declval<Linear>(), std::declval<Areal>())),
```

> Username: barendgehrels  
> Created_at: 2021-03-24 12:59:53 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r600453676  

interesting...

> Username: awulkiew  
> Created_at: 2021-03-25 02:21:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r600996110  

This looks differently now:  
```  
typedef distance::return_t<Linear, Areal, Strategies> return_type;  
```  
  
Are you ok with template aliases like this one?


📁 include/boost/geometry/strategies/distance/detail.hpp

```diff
  40 |+         && util::is_pointlike<Geometry2>::value
  41 |+      || util::is_segmental<Geometry1>::value
  42 |+         && util::is_segmental<Geometry2>::value
```

> Username: barendgehrels  
> Created_at: 2021-03-24 13:03:09 UTC  
> Updated_at: 2021-03-25 02:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r600455886  

Maybe this can get brackets?

> Username: awulkiew  
> Created_at: 2021-03-25 02:17:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/812#discussion_r600994951  

Done.


---
