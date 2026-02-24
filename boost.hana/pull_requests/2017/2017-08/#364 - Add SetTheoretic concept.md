# #364 [SetTheoretic] Add SetTheoretic concept [Open]

> Username: shreyans800755  
> Created at: 2017-08-20 22:02:27 UTC  
> Updated at: 2020-02-23 13:55:56 UTC  
> Url: https://github.com/boostorg/hana/pull/364  

It adds SetTheoretic concept for functions-  
union, intersection, difference, symmetric_difference.  
  
Closes https://github.com/boostorg/hana/issues/357

---

## Comment 1

> Username: shreyans800755  
> Created_at: 2017-08-21 17:43:31 UTC  
> Url: https://github.com/boostorg/hana/pull/364#issuecomment-323807451  

@ldionne Can you please review this ?

---

## Review 2 [Commented]

> Username: ricejasonf  
> Created_at: 2017-08-21 18:11:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/364#pullrequestreview-57571472  

📁 example/misc/infinite_set.cpp

```diff
  53 |+     struct SetTheoretic<infinite_set_tag> {
  54 |+         static constexpr bool value = true;
  55 |+     };
```

> Username: ricejasonf  
> Created_at: 2017-08-21 18:11:33 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r134295500  

If the purpose of the `SetTheoretic` concept is to guarantee that all associated functions are implemented, then being able to just opt in to use `union` in this case is probably incorrect.  
  
For this example, I would suggest either implementing the remaining functions or just make a custom `union` function. (not sure which is preferable)

> Username: shreyans800755  
> Created_at: 2017-08-21 18:17:58 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r134296745  

I was thinking it the other way around. If you want to use any of the set theoretic functions (at least one of 4 available at the moment), then it should have a concept `SetTheoretic`. Container doesn't need to implement all 4 operations IMO.  
Essentially it means that if you want to use any of these 4 functions, container must be `SetTheoretic`.

> Username: ricejasonf  
> Created_at: 2017-08-21 18:29:51 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r134298957  

Your documentation specifies a minimal complete definition including all four functions. I think this is what makes the concept useful, the guarantee that those functions work.

> Username: ldionne  
> Created_at: 2017-08-27 00:34:22 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400372  

The usual way of doing things in Hana is that if you satisfy the minimal complete definition, then you automatically model the concept, no need to specialize it. I think this is what we should do here. The only concept that requires specialization is `Sequence`, and that's because there's no additional syntax added on top of existing concepts, only semantics. But this is not the case here, so I'd like the model to be deduced automatically.

> Username: shreyans800755  
> Created_at: 2020-02-20 22:19:30 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r382290217  

Makes sense. Removing it.


---

## Review 3 [Changes requested]

> Username: ldionne  
> Created_at: 2017-08-27 00:48:18 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/hana/pull/364#pullrequestreview-58811822  

Great start, and thanks a lot for the work you're putting into it!

📁 include/boost/hana/fwd/concept/set_theoretic.hpp

```diff
  23 |+     //! Minimal complete definition
  24 |+     //! ---------------------------
  25 |+     //! `union_`, `intersection`, `difference` and `symmetric_difference`
```

> Username: ldionne  
> Created_at: 2017-08-27 00:13:36 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400140  

`symmetric_difference` can be derived from `union` and `difference`, see [this](https://en.wikipedia.org/wiki/Symmetric_difference#Properties). Hence, I don't think it should be part of the minimal complete definition.

> Username: shreyans800755  
> Created_at: 2020-02-20 22:20:26 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r382290614  

Makes sense. I think it should automatically kick in while calling difference first time if condition is not satisfied

---

📁 include/boost/hana/fwd/concept/set_theoretic.hpp

```diff
  30 |+     //!
  31 |+     //!
  32 |+     //! [1]: https://github.com/boostorg/hana/issues/357
```

> Username: ldionne  
> Created_at: 2017-08-27 00:14:04 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400144  

Why that link?

> Username: shreyans800755  
> Created_at: 2020-02-20 22:20:34 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r382290668  

removed

---

📁 include/boost/hana/fwd/concept/set_theoretic.hpp

```diff
  17 |+     //! @ingroup group-concepts
  18 |+     //! @defgroup group-SetTheoretic SetTheoretic
  19 |+     //! The `SetTheoretic` concept represents data structures supporting
```

> Username: ldionne  
> Created_at: 2017-08-27 00:25:02 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400277  

Defining the concept as "things that have the methods that the concept provides" is kind of circular, and not very useful IMO. Also, you're not defining laws for the concept. I'd go for a brief like this instead:  
  
> The `SetTheoretic` concept represents data structures that support an [algebra of sets][1].  
  
Then, I'd make some comments about what the concept is at a high level, and why it's useful. I'd also give the laws that the concept must follow, e.g. probably the ones on the Wikipedia page.  
  
Other comments:  
- One thing that must be thought about is the relationship between this concept and `Searchable`. Indeed, `Searchable` provides `is_subset`; it seems clear that there's an interesting mathematical link between both, and it should be explored, explained and some laws should be surfaced for data structures that are both `Searchable` and `SetTheoretic`.  
- You should also think about automatically-provided models and document them, if you can find some. What I mean here is that you should try to see whether satisfying some other concept(s) makes it possible to satisfy `SetTheoretic` automatically. When this is the case, Hana usually provides such a definition and documents it (e.g. `Foldable` for any `Iterable`).  
- The methods should be documented like normal methods that are tied to a concept, not individually for `map` and `set`.  
  
I know this may seem like a lot, but this is what a concept in Hana is; it's not only a syntactical construction, it's also a tool to reason mathematically about programs, which requires a little more work to define than "loose" concepts that don't have clear semantics, if I may put it that way.  
  
If there are things where you really don't know what to do, let me know and I'll try to make some time for researching the topic to try and provide some guidance.  
  
[1]: https://en.wikipedia.org/wiki/Algebra_of_sets

> Username: ricejasonf  
> Created_at: 2017-09-11 21:09:04 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r138192540  

Would it make sense for `SetTheoretic` to have its own accessors? Something like `get<T>(set)` == `at_key(set, T{})` would be very useful for `set` and it would be a way of making `SetTheoretic` provide an implementation for `Searchable`.  
  
It might even simplify implementation of these functions assuming "keys" are strictly looked up at compile-time. Hmm.. that might also require something like the `keys` function that `map` has.  
  
Also perhaps it should be just `hana::Set`.

> Username: ricejasonf  
> Created_at: 2017-09-11 21:16:59 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r138194365  

The TLDR is that they would only have to implement those accessors and not the union, intersection,... algorithms directly

> Username: DarthRubik  
> Created_at: 2018-05-23 21:17:41 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r190401992  

From what I see it does not necessarily make sense to have `SetTheoretic` be related to `Searchable` if the goal is to create a generic thing that supports the algebra of sets.  
  
You can make plenty of things that support the algebra of sets that it does not make sense to be `Searchable`.    
  
For example: a string of bits:  
      
    1010 union 0001 => 1011  
    1010 intersection 1000 => 1000  
    1010 difference 1000 => 0010  
    1010 symmetric_difference 1001 => 0011  
  
Anyways......thats just the way I see it.......  
@ldionne @ricejasonf @shreyans800755

> Username: ricejasonf  
> Created_at: 2018-05-24 16:43:33 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r190655449  

@DarthRubik I don't believe we are trying to couple `Set` with `Searchable`, but types that satisfy `Searchable` can be made to implement the needed functions for `Set`. For your string of bits you would probably implement those functions directly.


📁 include/boost/hana/intersection.hpp

```diff
  34 |+ 
  35 |+         static_assert(std::is_same<TX, TY>::value,
  36 |+         "hana::intersection(xs, ys) requires 'xs' and 'ys' to be SetTheoretic");
```

> Username: ldionne  
> Created_at: 2017-08-27 00:32:19 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400348  

The `static_assert` message is wrong; it should say "requires 'xs' and 'ys' to be of the same data type", or equivalent.

> Username: shreyans800755  
> Created_at: 2020-02-20 22:20:52 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r382290803  

fixed


📁 include/boost/hana/map.hpp

```diff
 212 |+     template <>
 213 |+     struct SetTheoretic<map_tag> {
 214 |+         static constexpr bool value = true;
```

> Username: ldionne  
> Created_at: 2017-08-27 00:37:51 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400419  

This should not be necessary.

> Username: shreyans800755  
> Created_at: 2020-02-20 22:21:04 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r382290875  

fixed


📁 include/boost/hana/set.hpp

```diff
  82 |+     template <>
  83 |+     struct SetTheoretic<set_tag> {
  84 |+         static constexpr bool value = true;
```

> Username: ldionne  
> Created_at: 2017-08-27 00:38:02 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400420  

This should not be necessary.


📁 include/boost/hana/symmetric_difference.hpp

```diff
  36 |+ 
  37 |+         static_assert(std::is_same<TX, TY>::value,
  38 |+         "hana::symmetric_difference(xs, ys) requires 'xs' and 'ys' to be SetTheoretic");
```

> Username: ldionne  
> Created_at: 2017-08-27 00:38:26 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400423  

The `static_assert` message is wrong; it should say "requires 'xs' and 'ys' to be of the same data type", or equivalent.

> Username: shreyans800755  
> Created_at: 2020-02-20 22:21:10 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r382290905  

fixed


📁 include/boost/hana/union.hpp

```diff
  34 |+ 
  35 |+         static_assert(std::is_same<TX, TY>::value,
  36 |+         "hana::union_(xs, ys) requires 'xs' and 'ys' to be SetTheoretic");
```

> Username: ldionne  
> Created_at: 2017-08-27 00:39:53 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400442  

The `static_assert` message is wrong; it should say "requires 'xs' and 'ys' to be of the same data type", or equivalent.


📁 include/boost/hana/difference.hpp

```diff
  35 |+ 
  36 |+         static_assert(std::is_same<TX, TY>::value,
  37 |+         "hana::difference(xs, ys) requires 'xs' and 'ys' to be SetTheoretic");
```

> Username: ldionne  
> Created_at: 2017-08-27 00:41:52 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r135400459  

The `static_assert` message is wrong; it should say "requires 'xs' and 'ys' to be of the same data type", or equivalent.

> Username: shreyans800755  
> Created_at: 2020-02-20 22:21:14 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r382290939  

fixed


---

## Comment 4

> Username: shreyans800755  
> Created_at: 2020-02-20 22:19:21 UTC  
> Url: https://github.com/boostorg/hana/pull/364#issuecomment-589380235  

Resuming work after ages. Would like complete it this time.

---

## Review 5 [Commented]

> Username: shreyans800755  
> Created_at: 2020-02-21 00:43:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/364#pullrequestreview-362326327  

📁 example/misc/infinite_set.cpp

```diff
  64 | 
  65 |+     template <>
  66 |+     struct difference_impl<infinite_set_tag> {
```

> Username: shreyans800755  
> Created_at: 2020-02-21 00:16:16 UTC  
> Updated_at: 2020-02-21 21:02:42 UTC  
> Url: https://github.com/boostorg/hana/pull/364#discussion_r382332175  

@ldionne   
This actually doesn't seem good to me logically. Although infinite set had union function implemented, we can't call it, because apparantly, union requires the class to be SetTheoretic meaning it requires minimal definition - all 3 functions union, intersection & difference. But, mathematically, it seems accurate though.  
Any thoughts ?


---

## Comment 6

> Username: shreyans800755  
> Created_at: 2020-02-23 13:55:56 UTC  
> Url: https://github.com/boostorg/hana/pull/364#issuecomment-590071282  

Also, mathematically, this doesn't seem accurate. Because, ideally intersection must be commutative, but here only set::intersection is commutative, but map::intersection is not.  
@ldionne I'm not sure if this was intended, but your input would be appreciated.

---
