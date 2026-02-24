# #350 Feature/map functions [Merged]

> Username: shreyans800755  
> Created at: 2017-05-25 19:41:32 UTC  
> Updated at: 2017-06-24 20:41:32 UTC  
> Merged at: 2017-06-24 19:58:00 UTC  
> Closed at: 2017-06-24 19:58:00 UTC  
> Url: https://github.com/boostorg/hana/pull/350  

Added intersection and difference functions for map along with docs.  
Closes https://github.com/boostorg/hana/issues/341

---

## Comment 1

> Username: shreyans800755  
> Created_at: 2017-05-25 20:41:29 UTC  
> Updated_at: 2017-05-25 20:52:44 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-304118319  

@ldionne I think I should also add symmetric_difference implementation for maps. I'll try to update the PR with its commits soon.  
EDIT: I think same implementation of sym_diff should work for both maps and sets.  
  
**One doubt:**  
Current implementation:  
symmetric_difference(x, y) = union(difference(x, y), difference(y, x))  
  
As you must be aware of the other implementation:  
symmetric_difference(x, y) = difference(union(x, y), intersection(x, y))  
  
Is former implementation is chosen randomly, or is it faster than later one ?

---

## Review 2 [Changes requested]

> Username: ldionne  
> Created_at: 2017-06-06 17:17:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/hana/pull/350#pullrequestreview-42378419  

Overall great, with a few nitpicks! Thanks a lot for the great PR!  
  
As for `symmetric_difference`, I think it would be quite nice to add it in this PR. I'm not sure why I chose one implementation over the other when I wrote it for `hana::set`.

📁 example/map/difference.cpp

```diff
   3 |+ // (See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)
   4 |+ 
   5 |+ #include <boost/hana/assert.hpp>
```

> Username: ldionne  
> Created_at: 2017-06-06 16:54:05 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120417389  

Please order includes.


📁 example/map/intersection.cpp

```diff
   3 |+ // (See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)
   4 |+ 
   5 |+ #include <boost/hana/assert.hpp>
```

> Username: ldionne  
> Created_at: 2017-06-06 16:57:29 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120418250  

x2


📁 include/boost/hana/fwd/map.hpp

```diff
 290 |+     //! In other words, the following holds for any object `pair(k, v)`:
 291 |+     //! @code
 292 |+     //!     pair(k, v) ^in^ intersection(xs, ys) if and only if (k, v) ^in^ xs && k ^in^ ys.keys()
```

> Username: ldionne  
> Created_at: 2017-06-06 16:59:08 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120418657  

`ys.keys()` does not exist; consider using `keys(ys)` instead

---

📁 include/boost/hana/fwd/map.hpp

```diff
 295 |+     //!
 296 |+     //! @note
 297 |+     //! This function is not commutative, i.e. `intersection(xs, ys)` is
```

> Username: ldionne  
> Created_at: 2017-06-06 17:03:56 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120419832  

Please reword to  
  
```c++  
//! This function is not commutative, i.e. `intersection(xs, ys)` is not  
//! necessarily the same as `intersection(ys, xs)`. Indeed, the set of keys  
//! in `intersection(xs, ys)` is always the same as the set of keys in  
//! `intersection(ys, xs)`, but the value associated to each key may be  
//! different. `intersection(xs, ys)` contains values present in `xs`, and  
//! `intersection(ys, xs)` contains values present in `ys`.  
```

---

📁 include/boost/hana/fwd/map.hpp

```diff
 324 |+     //! In other words, the following holds for any object `pair(k, v)`:
 325 |+     //! @code
 326 |+     //!     pair(k, v) ^in^ difference(xs, ys) if and only if (k, v) ^in^ xs && k ^not in^ ys.keys()
```

> Username: ldionne  
> Created_at: 2017-06-06 17:04:31 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120419957  

Same comment for `ys.keys()`.

---

📁 include/boost/hana/fwd/map.hpp

```diff
 329 |+     //!
 330 |+     //! @note
 331 |+     //! This function is not commutative, i.e. `difference(xs, ys)` is
```

> Username: ldionne  
> Created_at: 2017-06-06 17:05:10 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120420115  

"is not necessarily same" -> "is not necessarily _the_ same". Also, please add an explanation of why that's the case. Something like:  
  
```  
//! Indeed, consider the  
//! case where `xs` is empty and `ys` isn't. Then, `difference(xs, ys)`  
//! is empty but `difference(ys, xs)` is equal to `ys`. For the symmetric  
//! version of this operation, see `symmetric_difference`.  
```

---

📁 include/boost/hana/fwd/map.hpp

```diff
 334 |+     //!
 335 |+     //! @param xs, ys
 336 |+     //! Two maps to find difference.
```

> Username: ldionne  
> Created_at: 2017-06-06 17:06:44 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120420536  

Please change to: "Two maps to compute the difference of.". I'm using this formulation for `union_`; it might not be perfect but it'll be consistent.


📁 include/boost/hana/fwd/set.hpp

```diff
 234 | BOOST_HANA_NAMESPACE_END
 235 | 
 236 |+     //! Returns the difference of two sets.
```

> Username: ldionne  
> Created_at: 2017-06-06 17:09:04 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120421111  

Please keep the original documentation for `difference`.

> Username: shreyans800755  
> Created_at: 2017-06-06 20:39:50 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120474496  

If you're talking about lines removed in include/boost/hana/fwd/difference.hpp, then I think it makes sense to remove them, as we have different implementation for set and map. And we have different explanation/docs for that, too.

> Username: ldionne  
> Created_at: 2017-06-07 01:51:10 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120518998  

I just meant that the lines you removed should be the ones used for `hana::set`.


📁 include/boost/hana/map.hpp

```diff
  45 | #include <boost/hana/fwd/keys.hpp>
  46 | #include <boost/hana/fwd/union.hpp>
  47 |+ #include <boost/hana/fwd/intersection.hpp>
```

> Username: ldionne  
> Created_at: 2017-06-06 17:09:15 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120421151  

Please order includes.

---

📁 include/boost/hana/map.hpp

```diff
 544 |+         static constexpr auto apply(Xs&& xs, Ys&& ys) {
 545 |+             return hana::fold_left(
 546 |+                     static_cast<decltype(hana::keys(ys))&&>(hana::keys(ys)),
```

> Username: ldionne  
> Created_at: 2017-06-06 17:10:53 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120421581  

Instead, use `hana::keys(static_cast<Ys&&>(ys))`.

---

📁 include/boost/hana/map.hpp

```diff
 507 |+             // Second template param will be pair
 508 |+             // Get its key and check if it exists, if it does, insert key, value pair.
 509 |+             template<typename Result, typename Pair>
```

> Username: ldionne  
> Created_at: 2017-06-06 17:11:29 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120421707  

Space between `template` and `<typename`.

---

📁 include/boost/hana/map.hpp

```diff
 512 |+             }
 513 |+ 
 514 |+             template<typename Result, typename Pair>
```

> Username: ldionne  
> Created_at: 2017-06-06 17:11:35 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120421753  

x2


📁 test/map/difference.cpp

```diff
  11 |+ #include <support/minimal_product.hpp>
  12 |+ namespace hana = boost::hana;
  13 |+ using hana::test::ct_eq;
```

> Username: ldionne  
> Created_at: 2017-06-06 17:12:03 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120421868  

I don't think you need this.

---

📁 test/map/difference.cpp

```diff
  76 |+         ),
  77 |+         hana::make_map()
  78 |+     ));
```

> Username: ldionne  
> Created_at: 2017-06-06 17:13:58 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120422378  

Can you add a bigger test case with something like 10 elements in the maps?


📁 test/map/intersection.cpp

```diff
  11 |+ #include <support/minimal_product.hpp>
  12 |+ namespace hana = boost::hana;
  13 |+ using hana::test::ct_eq;
```

> Username: ldionne  
> Created_at: 2017-06-06 17:14:10 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r120422417  

Not needed.


---

## Comment 3

> Username: ricejasonf  
> Created_at: 2017-06-06 19:12:53 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-306588080  

What is the reason for `intersection` not being commutative. Is it a performance thing?

---

## Comment 4

> Username: shreyans800755  
> Created_at: 2017-06-06 20:35:00 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-306608961  

@ricejasonf `intersection` is done on the base of `keys` in case of `hana::map`, and not based on key and values, both. Frankly, I only thought this use case, and it didn't really struck into my head about intersecting two maps based on (key, value) pairs.  
  
> Is it a performance thing?  
  
Performance based on asymptotic analysis should be same if want to intersect based on (key, value), although the overhead of comparison of value will be increased.

---

## Comment 5

> Username: ricejasonf  
> Created_at: 2017-06-06 20:50:58 UTC  
> Updated_at: 2017-06-06 20:51:24 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-306613134  

@shreyans800755 Ah, right, it compares the keys. I wasn't even thinking about that. lol :sweat_smile:

---

## Comment 6

> Username: ldionne  
> Created_at: 2017-06-08 19:57:48 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-307210500  

One of the jobs failed with  
  
> /home/travis/build/boostorg/hana/include/boost/hana/fwd/set.hpp:263: warning: included file example/set/difference.cpp is not found. Check your EXAMPLE_PATH

---

## Review 7 [Commented]

> Username: ldionne  
> Created_at: 2017-06-09 04:03:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/350#pullrequestreview-43064071  

📁 include/boost/hana/map.hpp

```diff
  54 | #include <boost/hana/remove_if.hpp>
  55 | #include <boost/hana/second.hpp>
  56 |+ #include <boost/hana/tuple.hpp>
```

> Username: ldionne  
> Created_at: 2017-06-09 04:03:10 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r121045644  

Do you need `#include <boost/hana/tuple.hpp>` here?

> Username: shreyans800755  
> Created_at: 2017-06-09 18:59:41 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r121200056  

Corrected.  
I'll push symmetric_difference soon.


---

## Review 8 [Commented]

> Username: ldionne  
> Created_at: 2017-06-11 18:29:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/350#pullrequestreview-43325986  

📁 example/symmetric_difference.cpp

```diff
   5 | #include <boost/hana/assert.hpp>
```

> Username: ldionne  
> Created_at: 2017-06-11 18:29:08 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r121288276  

Just like we did for the other functions, would it be possible to have an `example/set/symmetric_difference.cpp` and an `example/map/symmetric_difference.cpp` file? This would be consistent with the rest of the lib, too.

> Username: shreyans800755  
> Created_at: 2017-06-11 18:40:05 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r121288423  

I thought of that, too. I chose this way because we don't have different implementations for set and map.


---

## Review 9 [Commented]

> Username: ldionne  
> Created_at: 2017-06-11 18:29:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/350#pullrequestreview-43325994  

📁 test/symmetric_difference.cpp

```diff
   1 |+ // Copyright Louis Dionne 2013-2017
```

> Username: ldionne  
> Created_at: 2017-06-11 18:29:37 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r121288284  

Same comment as for examples; it would be nice to have a test for map and a test for sets in different files.

> Username: shreyans800755  
> Created_at: 2017-06-11 18:40:54 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r121288433  

Same thought as example. So, I wasn't sure if we need two different files.


---

## Comment 10

> Username: shreyans800755  
> Created_at: 2017-06-11 18:44:21 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-307648402  

Regarding documentation of symmetric_difference, currently it exists only for set. Should I move it to `include/boost/hana/fwd/set.hpp` and add similar for map in `include/boost/hana/fwd/map.hpp` ?  
Or may be just adding a `@relatealso` tag with map in the same file `include/boost/hana/fwd/symmetric_difference.hpp` should do ?

---

## Comment 11

> Username: ldionne  
> Created_at: 2017-06-11 19:53:34 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-307652529  

I would duplicate the documentation for set and map. While duplication is generally bad, the rationale here is that the two implementations for set and map are conceptually unrelated, since they don't share a common concept. If we were to create a new concept and move `symmetric_difference` under it (and also probably others), then this is how we would de-duplicate the documentation. Does that make sense?

---

## Comment 12

> Username: shreyans800755  
> Created_at: 2017-06-11 20:54:39 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-307656176  

That makes sense. I'll push the changes.

---

## Review 13 [Commented]

> Username: ldionne  
> Created_at: 2017-06-16 16:05:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/350#pullrequestreview-44600510  

📁 test/set/symmetric_difference.cpp

```diff
   3 | // (See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)
   4 | 
   5 |- #include <boost/hana/assert.hpp>
```

> Username: ldionne  
> Created_at: 2017-06-16 16:05:20 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r122476822  

Those should stay, no?

> Username: shreyans800755  
> Created_at: 2017-06-16 18:01:10 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r122499753  

It is already coming from:  
test/set/symmetric_difference.cpp:10  
test/_include/laws/base.hpp:25  
test/_include/support/tracked.hpp:11  
  
And that's why tests are also passing.

> Username: ldionne  
> Created_at: 2017-06-18 19:28:35 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r122599019  

Yeah, but you should always include what you use to avoid relying on such dependencies, no?

> Username: shreyans800755  
> Created_at: 2017-06-18 19:31:34 UTC  
> Updated_at: 2017-06-18 19:39:15 UTC  
> Url: https://github.com/boostorg/hana/pull/350#discussion_r122599089  

Makes sense. Will update soon.


---

## Comment 14

> Username: ricejasonf  
> Created_at: 2017-06-24 20:07:27 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-310862821  

:shipit: :+1:

---

## Comment 15

> Username: ldionne  
> Created_at: 2017-06-24 20:08:30 UTC  
> Url: https://github.com/boostorg/hana/pull/350#issuecomment-310862869  

Thanks a lot @shreyans800755, this is a great PR!

---
