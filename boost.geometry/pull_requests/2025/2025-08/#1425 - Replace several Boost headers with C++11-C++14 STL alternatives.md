# #1425 Replace several Boost headers with C++11/C++14 STL alternatives. [Open]

> Username: tinko92  
> Created at: 2025-08-25 16:09:08 UTC  
> Updated at: 2026-02-17 14:52:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425  

This PR removes a number of includes of Boost headers that are no longer required with C++14 because there are either language constructs (e.g. `static_assert`) or standard library headers that fullfil similar functions (e.g. `std::ignore` instead of `boost::ignore_unused`).  
  
Where it wasn't needed code was removed entirely (e.g. `boost::ignore_unused` when the variable in question was passed to another function already). Where it seemed approriate, it was replaced with more explicit constructs (e.g. `static_asserts` on `std::is_convertible` in various concept headers rather than assignments to unused variables making it closer to the patterns established in C++ concepts).  
  
Some modernisations/cleanups are included that I noticed along the way (e.g. the usual `typedef` -> `using`, `some_type* some_name = 0;` -> `some_type* some_name = nullptr`, removal of `distance_projected_point_ax.hpp`, which was deprecated earlier).  
  
Because `std::ignore` is included it via `<tuple>` rather than `<utility>` so that it is compatible with older versions of the standard library (e.g. with GCC 11). This does not limit its intended usage with C++14 from today's perspective.  
  
No non-trivial refactoring was done in vararray. This type should maybe be replaced with `static_vector` (`inplace_vector` in C++26), which seems to be based on it and duplicates its functionality in Boost.Container. This was not included in this PR because it led to a performance regression.  
  
Some dependencies on Boost libraries now persist only for adapted geometry types. These were not touched because it would have changed documented behaviour. But IHMO it would be worth discussing whether these adapted geometries (Boost.Array and Boost.Tuple now, Boost.Range when the standard is raised to C++20 and Boost.Fusion when it is raised to C++26) could be deprecated for future removal, given that they are defined for types that are obsolete in C++14 which is the required minimum for Boost.Geometry.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-08-25 18:29:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1425#pullrequestreview-3152585266  

📁 include/boost/geometry/algorithms/detail/buffer/get_piece_turns.hpp

```diff
 289 |     inline bool apply(Section const& section1, Section const& section2,
 291 |-                     bool first = true)
 290 |+                     bool = true)
```

> Username: barendgehrels  
> Created_at: 2025-08-25 18:29:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#discussion_r2298814996  

So we can remove it. I'm fine with it like this in this PR, we can change it in a follow up


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-08-25 18:31:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1425#pullrequestreview-3152590035  

📁 include/boost/geometry/algorithms/detail/intersection/areal_areal.hpp

```diff
  83 |+                     Areal1, Areal2, single_out,
  84 |+                     point_tag, linestring_tag, polygon_tag
  85 |+                 >);
```

> Username: barendgehrels  
> Created_at: 2025-08-25 18:31:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#discussion_r2298818528  

This I don't like so much. `ignore_unused` was meant exactly to avoid this cryptic statement.  
Is it solveable in another way?  
  
(Why is the whole statement there anyway - but that is another question...)

> Username: tinko92  
> Created_at: 2025-08-25 18:50:45 UTC  
> Updated_at: 2025-08-25 18:51:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#discussion_r2298856873  

I will test taking a pointer of this type and assign it to ignore. I will also try to check why this statement was there, maybe it can be dropped.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-08-25 18:33:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1425#pullrequestreview-3152596280  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
  74 |+     static bool constexpr use_handle_as_touch = true;
  75 |+     static bool constexpr use_handle_as_equal = true;
  76 |+     static bool constexpr use_handle_imperfect_touch = true;
```

> Username: barendgehrels  
> Created_at: 2025-08-25 18:33:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#discussion_r2298822190  

Is `static constexpr` valid? I didn't check but I remembered there was something with it.  
Why is it actually changed? (A big PR can better have exactly one subject)

> Username: barendgehrels  
> Created_at: 2025-08-25 18:38:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#discussion_r2298832597  

But anyway, if it is valid, it is fine.  
Thanks for the PR, it seems a huge effort and a huge style improvement.

> Username: tinko92  
> Created_at: 2025-08-25 18:48:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#discussion_r2298852481  

I think, it changes the meaning in so far as that these values will not have actual storage, which I think is appropriate in this context, since I understood that these are never used in ways that requires taking their addresses but rather for decisions that can all be resolved at compile time. But I can just take it out of this PR tomorrow to have a more clear scope.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-08-25 18:34:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1425#pullrequestreview-3152598921  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_for_endpoint.hpp

```diff
 199 |     {
 200 |-         BOOST_STATIC_ASSERT(I < 2);
 200 |+         static_assert(I < 2, "I must not be greater than 1");
```

> Username: barendgehrels  
> Created_at: 2025-08-25 18:34:22 UTC  
> Updated_at: 2025-08-25 18:34:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#discussion_r2298823783  

This sounds like a funny sentence ;-)

> Username: tinko92  
> Created_at: 2025-08-25 18:41:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#discussion_r2298838356  

I chose a message like "I is out of bound" elsewhere, I think for the matrix extension. I should probably harmonize it in some way. Would "out of bound" be a fitting descriptor for any case where it e.g. exceeds the dimension?

> Username: barendgehrels  
> Created_at: 2025-08-25 19:59:53 UTC  
> Updated_at: 2025-08-25 20:00:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#discussion_r2299000440  

It is no problem, it just sounds like `I (me) should stay small` ;-)   
You can keep it.


---

## Review 5 [Approved]

> Username: barendgehrels  
> Created_at: 2025-08-25 18:41:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1425#pullrequestreview-3152616371  

I'm basically fine. Thanks a lot.  
  
Some suggestions:  
* split changes in `extensions` and other code, such that cherry-picking for `master` is more convenient. @vissarion to comment if this is still convenient, or it is done in another way  
* make it smaller. It was called `boost alternatives` but there are also other changes such as changing `typedef` to `using`. That is indeed tempting - but it makes the scope larger  
* I didn't review every line but more than half of it and it all makes sense to me (one question there)

---

## Comment 6

> Username: tinko92  
> Created_at: 2025-08-25 19:00:12 UTC  
> Updated_at: 2025-09-03 12:48:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#issuecomment-3221399312  

Thanks for the very quick review! 🙏 I will update the PR to address the points you mentioned.  
  
Edit: Some unexpected time constraints came up, I'll try to update the PR by the end of next week.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2025-08-25 20:05:07 UTC  
> Updated_at: 2025-08-25 20:05:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#issuecomment-3221578569  

> Thanks for the very quick review! 🙏 I will update the PR to address the points you mentioned.  
  
I didn't mean you should revert the `typedef` -> `using`. That is more for a next similar PR. It can now stay in this PR.  
  
But splitting out extensions would still be convenient, I think.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2025-10-09 18:03:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#issuecomment-3386971373  

@tinko92 do you agree the extensions should get into a separate commit? Is that possible?

---

## Comment 9

> Username: tinko92  
> Created_at: 2025-10-10 00:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1425#issuecomment-3387850367  

@barendgehrels Yes, I agree. Sorry for not having updated this in a while. I'll update this based on the latest develop state as soon as I find time and seperate it into two commits.

---

## Review 10 [Approved]

> Username: vissarion  
> Created_at: 2025-12-08 15:28:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1425#pullrequestreview-3552640358  

Looks good to me! Nice modernization, Tinko, thanks! I am OK if the code splits in two commits (to separate extensions code).

---
