# #84 Feature/distance [Merged]

> Username: mkaravel  
> Created at: 2014-07-02 21:00:31 UTC  
> Updated at: 2014-07-10 21:35:37 UTC  
> Merged at: 2014-07-10 21:35:37 UTC  
> Closed at: 2014-07-10 21:35:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/84  

Change the return type of `distance` and `comparable_distance` free functions.  
In more detail:  
- `distance` returns `distance_result<...>::type` or `default_distance_result<...>::type`  
- `comparable_distance` returns `comparable_distance_result<...>::type` or `default_comparable_distance_result<...>::type`

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-07-08 09:48:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/84#issuecomment-48292234  

The last 7 commits (not counting the merge commit) refer to  
1. replacing `result_of::distance` by `distance_result`  
2. adding full variant support for comparable distances  
3. adding test cases for comparable distance variant support

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-07-08 10:38:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/84#issuecomment-48320808  

I'm guessing that `default_comparable_strategy` was added to distinguish between `distance()` and `comparable_distance()` in some specializations, e.g. in `resolve_strategy::distance_result<>`.  
But wouldn't the use of `default_strategy` in all algorithms be more consistent?  
In the case of `resolve_strategy::distance_result<>` e.g. the implementation of corresponding `resolve_strategy::comparable_distance_result<>`.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-07-08 12:01:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/84#issuecomment-48326981  

@awulkiew Your guess is correct. I wanted to send an email about this, but since you mentioned it, I am going to comment here.  
  
`distance` is different when compared to other algorithms. You can pass two different types of strategies: regular (let's call them this way) and comparable. Depending on the type of strategy that you pass you either get the regular or comparable distance:  
  
```  
G1 g1; // initialization not shown  
G2 g2; // initialization not shown  
  
// the following gives regular distance  
regular_strategy_type regular_strategy;  
bg::distance_result<G1, G2, regular_strategy>::type d = bg::distance(g1, g2, regular_strategy);  
  
// the following gives comparable distance  
comparable_strategy_type comparable_strategy;  
bg::distance_result<G1, G2, comparable_strategy>::type cd = bg::distance(g1, g2, comparable_strategy);  
```  
  
Now if you want to pass the default strategy to distance, you need to distinguish between the default regular strategy and the default comparable strategy. So using the default strategies, the above code would have been:  
  
```  
G1 g1; // initialization not shown  
G2 g2; // initialization not shown  
  
// the following gives regular distance  
bg::distance_result<G1, G2, default_strategy>::type d = bg::distance(g1, g2, default_strategy());  
  
// the following gives comparable distance  
bg::distance_result<G1, G2, default_comparable_strategy>::type cd = bg::distance(g1, g2, default_comparable_strategy());  
```  
  
which is, BTW, also equivalent to:  
  
```  
G1 g1; // initialization not shown  
G2 g2; // initialization not shown  
  
// the following gives regular distance  
bg::default_distance_result<G1, G2>::type d = bg::distance(g1, g2);  
  
// the following gives comparable distance  
bg::default_comparable_distance_result<G1, G2>::type cd = bg::comparable_distance(g1, g2);  
```  
  
I gave it some thought as to how to have `distance` return both regular and comparable distances, and I could not find a way to do it with a single default strategy (IMHO this makes distance different from any other algorithm).  
  
So the solution I came up with was to introduce default_comparable_strategy.  
  
The other option, of course, was to implement comparable_distance_result just like distance_result, but it felt like I would have <b>a lot</b> of code duplication without any particular reason.  
  
On top of that, your suggestion could solve the issue with respect to comparable_distance_result, but not really with respect to computing the actual comparable distance: `comparable_distance` calls `distance` internally  with an appropriate strategy, so if a comparable strategy is not passed by the user (I mean the user calls the strategy-less `comparable_distance` function) I would need to call `distance` with an appropriate default comparable strategy, which is what I am doing now with `default_comparable_strategy`.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-07-08 12:29:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/84#issuecomment-48329412  

If I understand correctly, without it you'd be forced to implement the whole Variant support in `comparable_distance()` nearly exactly the same way how it's implemented for `distance()`.  
So yes, it's consistent with the rest of the `distance()`/`comparable_distance()` implementation which indeed is unique.  
Since it's an implementation detail used in Variant support, not exposed to the user, we probably musn't worry about it so much. But lets wait to see what others have to say.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-07-08 14:01:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/84#issuecomment-48339632  

Yes, this is the problem exactly. I wanted to avoid implementing variant support for `comparable_distance` by essentially copying/pasting the `distance` variant support implementation and simply changing the names of the classes.  
  
Yes, let's wait for more opinions on this. Bruno has been involved heavily with variant support, so I would expect him to have an opinion on this approach.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-07-10 18:51:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/84#issuecomment-48647623  

The prefered solution was to reimplement Variant support in `comparable_distance()` implementation and to use `default_strategy`. So AFAIU it's now ok to merge which I'm planning to do later today if nobody has any objections.

---

## Comment 7

> Username: brunolalande  
> Created_at: 2014-07-10 21:19:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/84#issuecomment-48665898  

Yep, OK for me.  
  
Bruno

---
