# #317 Fix less functor for turns [Merged]

> Username: mkaravel  
> Created at: 2015-07-24 07:35:37 UTC  
> Updated at: 2015-08-03 05:47:53 UTC  
> Merged at: 2015-07-29 07:13:53 UTC  
> Closed at: 2015-07-29 07:13:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/317  

This PR fixes a problem with less functor for turns.  
  
In the previous implementation the fractions of the two turns where compared first using `operator<` and then tested for equality using `math::equals`. The consequence of this implementation is that it could be possible to have two turns `t1` and `t2`, whose fractions satisfied both `operator<` and `math::equals`, which lead to the possibility of having both `less(t1, t2)` and `less(t2, t1)` be `true`. This behavior for less is `wrong` and has produced failures on various compilers (especially when sorting).  
  
The solution is to rearrange the code in the less functor so that `math::equals` for the two fractions is checked first, that is before the `operator<` is called; this makes the outcomes of `less(t1, t2)` and `less(t2, t1)` always consistent with each other.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-07-24 13:18:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/317#issuecomment-124517521  

I'm OK, thanks for the fix

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-07-24 13:32:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/317#issuecomment-124521232  

I of course agree if that fixes a bug. However I'm wondering, is the `equals()` here needed at all? We should probably be consistent in ways we compare turns. E.g. in the implementation of `relate()` I'm not using `equals()` for , just `operator<` and `operator==` which result shouldn't overlap (https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/relate/turns.hpp#L274). Both less-comparators should be consistent either way. Actually, we should probably have only one.  
  
First let's see what is happening internally. Ratios are of type `segment_ratio` so `operator<` and `operator==` of this type are called (`equals()` uses `operator==` of user-defined type). So your patch just changes the sequence of condition checks and indeed gives you the expected results. The question is: why? `operator<` should basically be consistent with `operator==`. The answer is that it's because `equals()` is used internally in the `segment_ratio::operator==` but not used in `segment_ratio::operator<`. This of course makes these operators not consistent.  
  
IMO the correct thing to do would either be to fix the `segment_ratio::operator<` to be consistent with `operator==` using `math::equals()` in `operator<` or remove `math::equals()` from `operator==` to make it consistent with `operator<` but then provide an alternative so e.g. overload/specialize `math::equals()` for `segment_ratio` allowing to compare ratios according to some EPS. The second solution would be the closest to the built-in type behavior and I guess the ratios should probably behave like any other numerical type, e.g. `double`.  
  
Then we should think how ratios/fractions should be equal-compared in `use_fraction`, using EPS or not? If the answer was yes then we should keep your fix and change the version used in `relate()`, if not then it wouldn't matter what would be the order of checks.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-07-28 18:44:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/317#issuecomment-125711505  

I have modified the version of `less` used for turns in `relate()`. See commit 21c143e.  
  
Other than that, I suggest to leave the rest as they are.  
Adam, Barend are you okay with this?

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-07-28 18:52:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/317#issuecomment-125715869  

BTW, all algorithm unit tests pass with the latest changes.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-07-28 19:04:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/317#issuecomment-125722264  

Thanks! I'm ok with leaving the rest as it is for now, since the purpose of this PR is fixing turn less comparisons, not the implementation of `segment_ratio`.

---
