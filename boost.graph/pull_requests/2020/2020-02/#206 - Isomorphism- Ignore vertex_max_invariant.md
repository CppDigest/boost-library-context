# #206 Isomorphism: Ignore vertex_max_invariant [Merged]

> Username: jan-grimo  
> Created at: 2020-02-16 10:23:34 UTC  
> Updated at: 2023-07-18 08:32:18 UTC  
> Merged at: 2023-07-18 05:06:54 UTC  
> Closed at: 2023-07-18 05:06:54 UTC  
> Url: https://github.com/boostorg/graph/pull/206  

Isomorphism: Ignore `vertex_max_invariant`  
- `vertex_max_invariant` and `invariant2.max()` are misnomers since what is  
  expected is an upper exclusive bound on the possible invariant values,  
  not their maximum value.  
- The parameter can be ignored and the upper exclusive bound found  
  cheaply at the start of `test_isomorphism`  
- Removes the additional requirement of a nullary `max` member function on  
  `invariant2`  
  
Related to #203   
  
If this PR is accepted, then the range requirement on vertex invariants can be removed entirely with another PR.

---

## Comment 1

> Username: joemalle  
> Created_at: 2021-06-13 02:47:35 UTC  
> Url: https://github.com/boostorg/graph/pull/206#issuecomment-860142146  

What do you think of using an unordered_map/similar instead of a vector to keep track of multiplicities?

---

## Comment 2

> Username: jan-grimo  
> Created_at: 2021-06-14 08:51:14 UTC  
> Url: https://github.com/boostorg/graph/pull/206#issuecomment-860514246  

> What do you think of using an unordered_map/similar instead of a vector to keep track of multiplicities?  
  
It's been a while since I was read up on the algorithm, but if I'm not completely mistaken, I did exactly that in #204. Using an unordered map for the multiplicities was kind of the final point of that MR (I wanted to use a perfect hashing function for a vertex invariant), but I was advised to split that one into smaller bits for better reviewing. Unfortunately, no progress on any of them since.  
  
Since nobody else seemed to have the expectation that the isomorphism would map disconnected vertices, I fixed it outside of the isomorphism call where I needed it. Seeing you face the same surprise, maybe we should be a bit more assertive about asking for review.

---

## Review 3 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-07-07 01:43:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/206#pullrequestreview-1517608639  

📁 doc/isomorphism.html

```diff
 133 |- return <tt>num_vertices(g2) * (num_vertices(g2)+1)</tt>.<br>
 134 |- <b>Python</b>: Unsupported parameter.
 127 |+ This parameter is ignored.
```

> Username: jeremy-murphy  
> Created_at: 2023-07-07 01:43:06 UTC  
> Url: https://github.com/boostorg/graph/pull/206#discussion_r1255112688  

Please just add a touch more detail to explain why it is ignored.


---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2023-07-07 01:44:17 UTC  
> Url: https://github.com/boostorg/graph/pull/206#issuecomment-1624519742  

Again, sorry about the long wait times on review. It is a case of too many issues, not enough reviewers.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2023-07-07 01:46:11 UTC  
> Url: https://github.com/boostorg/graph/pull/206#issuecomment-1624521236  

@joemalle , are you able to review this functionality?

---

## Review 6 [Changes requested]

> Username: joemalle  
> Created_at: 2023-07-07 15:59:05 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/206#pullrequestreview-1519206180  

This looks fine to me other than the initialization issue.  It would be nice to add a test

📁 include/boost/graph/isomorphism.hpp

```diff
 164 |             f[v] = graph_traits< Graph2 >::null_vertex();
 165 | 
 166 |+             std::size_t max_invariant;
```

> Username: joemalle  
> Created_at: 2023-07-07 15:58:27 UTC  
> Updated_at: 2023-07-07 15:59:05 UTC  
> Url: https://github.com/boostorg/graph/pull/206#discussion_r1256050792  

This variable is used uninitialized.  `-Wall` would catch this.  Suggest using ` = 0;` or something along those lines

> Username: jan-grimo  
> Created_at: 2023-07-09 08:24:49 UTC  
> Updated_at: 2023-07-09 08:34:58 UTC  
> Url: https://github.com/boostorg/graph/pull/206#discussion_r1257437872  

I've added the initialization.  
  
I do think you're incorrect, though. `max_invariant` is never read from before being assigned a value, and adding `-Wall` does not show any `-Wmaybe_uninitialized` notices.

> Username: jeremy-murphy  
> Created_at: 2023-07-17 07:40:46 UTC  
> Updated_at: 2023-07-17 07:40:47 UTC  
> Url: https://github.com/boostorg/graph/pull/206#discussion_r1264984931  

I agree with @jargonzombie, but I think since we'll ultimately go with the `unordered_map` solution then the argument here is moot.


---

## Review 7 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-07-17 01:42:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/206#pullrequestreview-1531845717  

📁 include/boost/graph/isomorphism.hpp

```diff
 183 |+                 // called, so the invar?_arrays cannot be empty, so back() is
 184 |+                 // safe. Also the two invariant arrays are equal:
 185 |+                 max_invariant = invar1_array.back() + 1;
```

> Username: jeremy-murphy  
> Created_at: 2023-07-17 01:42:35 UTC  
> Url: https://github.com/boostorg/graph/pull/206#discussion_r1264784544  

What if `invar1_array.back() == std::numeric_limits<std::size_t>::max()`?  
  
It will happen eventually.  
  
I had a quick look through the whole code for isomorphism but it's not obvious to me exactly what the simplest way of dealing with this is.  
  
One option is that we only support values less than `std::numeric_limits<std::size_t>::max()` in the array and guard against it defensively using `assert`. I think this is reasonable.

> Username: jeremy-murphy  
> Created_at: 2023-07-17 01:43:07 UTC  
> Updated_at: 2023-07-17 01:43:08 UTC  
> Url: https://github.com/boostorg/graph/pull/206#discussion_r1264784634  

But I'm totally open to other suggestions.

> Username: jan-grimo  
> Created_at: 2023-07-17 07:31:40 UTC  
> Url: https://github.com/boostorg/graph/pull/206#discussion_r1264976656  

Well, there is the documentation change from  
  
```diff  
- The values returned by these two functions must be in the range [0, <tt>vertex_max_invariant</tt>).  
+ The values returned by these two functions must have a low upper bound.   
```  
  
The reason that's there is not the possibility of overflow, but rather the [invariant multiplicity counting](https://github.com/jargonzombies/graph/blob/ec286110269fc297bd3b3b6b687379b7d0afd8d3/include/boost/graph/isomorphism.hpp#L192), which allocates a zeroed vector of length equal to `max_invariant`, which is what I found when I encountered an out-of-memory error in trying a colored graph isomorphism with hashed invariants, prompting #203.  
  
So to some degree I think adding an `assert` papers over a more fundamental issue that might want addressing.  
  
The fix I would suggest is a [multiplicity counting using `unordered_map`](https://github.com/boostorg/graph/pull/204/files#diff-23655e4e06fd65a235692ad0debfc5c08c59dc6ba4cd4b27424c66df437b0bd8R177) instead, which eliminates `max_invariant` entirely, and allows consumers to use the full `size_t` range in their invariants without allocating excessive memory. Assuming this PR and #206 (which introduces `unordered_map` ) are accepted, I would have introduced another PR adding the remaining bits of #204, which, besides the counting, are just hashable concept checks on the invariant function result type and documentation changes.  
  
I think there are two options:  
  
- We add the multiplicity counting algorithm here.  
- We add the assert and expand the documentation here, mentioning the allocation to explain the low bound documentation addition. If the follow-up PR with multiplicity counting gets accepted, that gets removed again.  
  
I'm going to go with the latter for now. Let me know if you disagree.

> Username: jeremy-murphy  
> Created_at: 2023-07-17 07:39:10 UTC  
> Url: https://github.com/boostorg/graph/pull/206#discussion_r1264983485  

I'm inclined to trust your judgement here and go with whatever is the most efficient way for you to get it done. Sorry about wasting time with splitting the PR into multiple pieces, but at the time I felt like I wasn't going to be able to process it all at once.

> Username: jan-grimo  
> Created_at: 2023-07-18 08:32:17 UTC  
> Url: https://github.com/boostorg/graph/pull/206#discussion_r1266424109  

I do think it's better split up the way it is, so no worries. The original PR did do a lot of things at once.


---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2023-07-18 05:07:22 UTC  
> Url: https://github.com/boostorg/graph/pull/206#issuecomment-1639481528  

Done, thank you!

---
