# #376 Eytzinger search [Closed]

> Username: jhcarl0814  
> Created at: 2022-12-24 04:56:12 UTC  
> Updated at: 2023-01-12 16:01:31 UTC  
> Closed at: 2023-01-12 16:01:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/376  

design decisions (very very questionable):  
  
1. `vec_` is outside of `sorted_array_and_binary_search` and `eytzinger_layout_and_eytzinger_binary_search` to make it easier for outsiders to get `vec_` without knowing the type of `data_structure_` used  
2. so rebuilding the `data_structure_` is needed whenever `vec_` constructs or changes (forgetting to add copy constructor, move constructor, copy assignment operator and move assignment operator causes hard-to-track bugs), and  
3. `sorted_array_and_binary_search` and `eytzinger_layout_and_eytzinger_binary_search` each gets an extra `constructor` and an extra `assign` to make `variable`'s move constructor and move assignment operator `noexcept`.  
    - `sorted_array_and_binary_search`'s does re-construction from `vec_` and ignores `rhs` because it's shallow;  
    - `eytzinger_layout_and_eytzinger_binary_search`'s does `std::move` from `rhs` and ignores `vec_` because it has state and the function needs to be `noexcept`.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2022-12-24 08:46:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1364489601  

Thanks for the patch. This changes a lot of things that are unrelated to the feature. Please split this patch and put the cosmetic changes (wrapping min/max etc) in another PR.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2022-12-24 08:58:07 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1364490866  

Don't make the data structure a template argument. It is fine to replace the standard implementation, because the speed is essentially the same and the additional memory overhead is small. The core logic of the eytzinger search should be implemented in a seperate struct or class, as it is now, so that the core algorithm can be tested in isolation (and potentially reused).  
  
Perhaps the vector should be merged into our eytzinger class, although this may have unwanted side effects for the design.

---

## Comment 3

> Username: jhcarl0814  
> Created_at: 2022-12-24 10:05:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1364502423  

@HDembinski Thank you for simplifying all this! Now I can get rid of the four special members and the extra `ctor` and extra `assign`.

---

## Comment 4

> Username: jhcarl0814  
> Created_at: 2022-12-24 10:27:45 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1364505301  

The cosmetic changes (wrapping min/max etc) are split into https://github.com/boostorg/histogram/pull/377 .

---

## Review 5 [Commented]

> Username: HDembinski  
> Created_at: 2022-12-24 11:55:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/376#pullrequestreview-1229614395  

📁 include/boost/histogram/detail/nonmember_container_access.hpp

```diff
  46 | }
  47 | 
  48 |+ #if __cpp_lib_nonmember_container_access >= 201411L
```

> Username: HDembinski  
> Created_at: 2022-12-24 11:55:42 UTC  
> Updated_at: 2022-12-24 12:07:13 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#discussion_r1056806007  

This is also a cosmetic change, please revert.

> Username: jhcarl0814  
> Created_at: 2022-12-24 21:48:28 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#discussion_r1056875941  

Got it. Reverted.  
  
---  
  
(The original intent of this change is to use unqualified `size` inside `include\boost\histogram\detail\eytzinger_search.hpp` to let `size` resolve to user provided one (if there is one) through adl.)  
  
- Before C++17 there is no `std::size` so I `#include<boost/histogram/detail/nonmember_container_access.hpp>`.  
- From C++17 `std::size` conflicts with `detail::size` **when I write unqualified `size`** so I added `#if`.  
  
I checked the doc just now. Seems the `size` is intended to be a customization point, so if I use `detail::size` then there is no adl then the customizability of `size` will be lost.  
https://en.cppreference.com/w/cpp/iterator/size says:  
> Custom overloads of size may be provided for classes and enumerations that do not expose a suitable size() member function, yet can be detected.


---

## Review 6 [Commented]

> Username: HDembinski  
> Created_at: 2022-12-24 12:02:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/376#pullrequestreview-1229615277  

📁 include/boost/histogram/axis/variable.hpp

```diff
 100 |+         return vec;
 101 |+       }())
 102 |+       , eytzinger_layout_and_eytzinger_binary_search_(vec_) {
```

> Username: HDembinski  
> Created_at: 2022-12-24 12:02:22 UTC  
> Updated_at: 2022-12-24 12:03:45 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#discussion_r1056806965  

The trick with the lambda is a bit too clever for my taste. I think it is more readable if we keep the code in the body of the constructor. This also does not provide the strong exception guarantee yet. Once the ctor provides the guarantee, that should also be stated in the docstring. `metadata_type` would have to become a forward reference for this to work.  
  
Ok, since it is not trivial to achieve, we also break that into a separate PR. Let's keep this PR focussed on implementing eytzinger search.

> Username: jhcarl0814  
> Created_at: 2022-12-24 22:09:40 UTC  
> Updated_at: 2022-12-24 22:09:41 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#discussion_r1056877290  

`eytzinger_layout_and_eytzinger_binary_search_`'s initialization needs an initialized `vec_`.  
If we move the intitialization code of `vec_` into constructor body, then `eytzinger_layout_and_eytzinger_binary_search_` will default initializes into a dummy state and then initializes again using `eytzinger_layout_and_eytzinger_binary_search_t& assign(const Range& r)`, is that acceptable?


---

## Comment 7

> Username: HDembinski  
> Created_at: 2022-12-24 12:06:18 UTC  
> Updated_at: 2022-12-24 12:13:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1364519292  

> `vec_` is outside of `sorted_array_and_binary_search` and `eytzinger_layout_and_eytzinger_binary_search` to make it easier for outsiders to get `vec_` without knowing the type of `data_structure_` used  
  
We don't allow direct access to the array, so it is fine if we internally change the data structure from vector_type to something else. However, users of `variable` must be able to look up a bin by index resonably efficiently. This happens through  
```c++  
auto bin(index_type idx) const noexcept { return interval_view<variable>(*this, idx); }  
```  
A generic iterator and begin() and end() are generated through mixin classes, which call this interface.  
  
In most cases, users will iterate over the values in their original order, so if we need to make performance trade-offs, this is the case we should focus on. Ideally, we keep only the eytzinger-sorted array and not the original array.

---

## Comment 8

> Username: jhcarl0814  
> Created_at: 2022-12-24 21:59:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1364588317  

@HDembinski According to https://www.hyrumslaw.com/ the users of `auto bin(index_type idx)` might have already depended on it being `O(1)`. If we remove the original `vec_` and modify `value_type value(real_index_type i)` to use `i` to locate the bin in the eytzinger complete binary tree, the time complexity becomes `O(lg(n))` and will break the previously not documented "interface".

---

## Comment 9

> Username: HDembinski  
> Created_at: 2022-12-26 09:31:05 UTC  
> Updated_at: 2022-12-26 09:36:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1365034530  

> @HDembinski According to https://www.hyrumslaw.com/ the users of `auto bin(index_type idx)` might have already depended on it being `O(1)`. If we remove the original `vec_` and modify `value_type value(real_index_type i)` to use `i` to locate the bin in the eytzinger complete binary tree, the time complexity becomes `O(lg(n))` and will break the previously not documented "interface".  
  
That's all true, however:  
1) We do not consider that a breaking change in Boost. Breaking changes are those which make user code not compile anymore and we do not violate any explicit guarantee given in the documentation. The complexity of bin value look-up was not defined in the documentation, which means it is implementation defined and can change at any time.  
2) For the small N that we deal with here, the difference between O(1) and O(log(N)) should not be dramatic.  
3) In the typical use of a histogram, you need bin index look-up to be very fast because that's performed in a hot loop, but bin value look-up does not happen in a hot loop.  
  
I am more concerned about the increase in memory from holding two arrays. We already need two arrays instead of one in the current implementation, to hold the eytzinger-sorted values and the indices. Adding yet another array makes the implementation unfavorable in my view. The performance increase of eytzinger is not so dramatic that tripling the amount of required memory seems worth it.  
  
If the cons outweigh the pros, I would abandon this change.

---

## Comment 10

> Username: HDembinski  
> Created_at: 2023-01-11 10:51:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1378564786  

@jhcarl0814 I have been thinking about this change and after all, the costs do seem to outweigh the benefits. Eytzinger users more storage, iteration over the axis will become slower, and the benefits are only substantial if you make a very large axis that we do not encounter in real life. In many cases, the axis should fit into the L1 cache, and then the Eytzinger layout should not provide any benefits.

---

## Comment 11

> Username: jhcarl0814  
> Created_at: 2023-01-12 10:46:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1380140718  

@HDembinski You are right.  
  
- Eytzinger layout does not have the ability to restore origin index from Eytzinger index quickly which leads to adding a index table and using too much memory.  
- Eytzinger layout does not have the ability to `push_back` (or `push_front`) quickly like `std::vector` (or `std::deque`) because it was designed to be a search-only data structure.  
  
I should have done more study before suggesting this thing. Sorry for taking up your time.

---

## Comment 12

> Username: HDembinski  
> Created_at: 2023-01-12 16:01:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/376#issuecomment-1380614047  

> I should have done more study before suggesting this thing. Sorry for taking up your time.  
  
No need to apologize. I could equally apologize to you, since I asked you to prepare a PR too early. We could have figured out these caveats without the PR. Thank you anyway, this was very interesting for me.

---
