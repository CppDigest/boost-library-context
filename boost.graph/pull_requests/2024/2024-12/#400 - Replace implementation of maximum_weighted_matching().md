# #400 Replace implementation of maximum_weighted_matching() [Merged]

> Username: jorisvr  
> Created at: 2024-12-05 18:57:58 UTC  
> Updated at: 2025-02-26 09:21:02 UTC  
> Merged at: 2025-02-10 02:26:53 UTC  
> Closed at: 2025-02-10 02:26:53 UTC  
> Url: https://github.com/boostorg/graph/pull/400  

This is a re-implementation of maximum_weighted_matching, based on a paper by Zvi Galil.  
The new code runs in time O(V^3).  
A new set of testcases are also added.  
  
Resolves #199 #223 #399  
  
The code has been tested extensively on large random graphs, using LEMON as a reference.  
  
Faster algorithms are known for this problem. I initially planned to implement the O(V*E*log(V)) algorithm by Galil, Micali and Gabow. However it needs a mutable heap with features that are not readily available in the BGL, and it needs a special kind of mergeable priority queue. While possible, I feel that the amount of code would be disproportionate. So I decided to fall back to a simpler O(V^3) algorithm, essentially the same algorithm that inspired the previous implementation.   
  
Feedback is very welcome. I will already mention a few points that may draw criticism:  
- I kept `brute_force_maximum_weighted_matching()` unchanged. This function is not very useful in my opinion, but it was part of the public API and there is no need to change it.  
- The documented API of `maximum_weighted_matching()` is backwards compatible with the previous code. But I removed the class `weighted_augmenting_path_finder`, which was essentially an internal detail although it lived in the global `boost` namespace.  
- I do runtime checks on some aspects of the input graph (vertex indices and range of edge weights). I don't see this much in the BGL and I guess it may be frowned upon. The thing is, the code will trigger undefined behavior if these preconditions are violated, and I feel like I can't let that happen.  
- Once a matching has been computed, a separate (much faster) algorithm can verify that the matching is optimal. If the primary algorithm is correct, this verification will never fail. I enabled the verification step by default, even though it is redundant and never fails. The primary algorithm is tricky. I feel that the certainty provided by the verification step is worth more than the clock cycles it costs.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-01-14 09:19:47 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2589402573  

Are the heap data structures in Boost.Heap not sufficient? They are mergeable and mutable.  
However, even if they are sufficient, I would be happy to simply get a correct implementation first that fixes the bugs and get a fast implementation later.   
What do you think?

---

## Comment 2

> Username: jorisvr  
> Created_at: 2025-01-14 12:24:38 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2589779567  

> Are the heap data structures in Boost.Heap not sufficient?  
  
The mutable heap in Boost.Heap would work as the "plain" type of heap in the matching algo. But it looks like BGL currently does not use Boost.Heap and I don't know how you feel about adding that dependency.  
  
I also need a concatenable heap which does not currently exist in Boost. The merge feature of Boost.Heap is not sufficient. I need to merge heaps in O(log(n)) time with the option to unmerge them later in O(log(n)). The typical way to implement this is with a custom balanced binary tree. It's not rocket science but it adds another 800 lines or so.  
  
LEMON and LEDA implement the O(V E log(V)) matching algorithm. It is much faster than O(V^3) on certain classes of sparse graphs. The speedup on random sparse graphs is fairly modest in my experience. And it can be slower on dense graphs.  
  
The new code is already an order of magnitude faster than the previous version for graphs with V > 200. My feeling is that the faster algorithm adds a lot of code in exchange for little benefit. But I'm up for the challenge. If you want the best matching algorithm in BGL, I will be happy to work on it.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2025-01-15 00:37:25 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2591391464  

Thanks for the explanation. There's no problem with adding Boost.Heap as a dependency, as Boost.Graph already depends on many other parts of Boost. Sounds like the efficient algorithm would require adding a new data structure to Boost.Heap to start with, which shouldn't be too difficult, although I'm aware that the maintainer is not all that active any more. Given that the new implementation is much faster anyway, let's defer the efficient algorithm to later. Ultimately it would be nice to have a top-level algorithm that uses a heuristic to pick the fastest algorithm but users are still free to call specific algorithms. (Best of both worlds.)  
Ok, now I have to find time to look over this code. Honestly, it could take a couple of weeks, so please be patient.  
Thanks for the work!

---

## Comment 4

> Username: jorisvr  
> Created_at: 2025-01-15 08:02:53 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2591879114  

> Sounds like the efficient algorithm would require adding a new data structure to Boost.Heap to start with  
  
I think the concatenable queue may be so special-purpose that it could just stay in BGL, but generalizing it is definitely also a valid option.  
  
> Given that the new implementation is much faster anyway, let's defer the efficient algorithm to later.  
  
Agreed. It occurs to me that the O(V E log(V)) algorithm also needs an `edge_index` property, which breaks backward compatibility. There may be ways to deal with this, but it seems like it will be a more difficult road than the current PR.  
  
> Ok, now I have to find time to look over this code.  
  
I understand. There is no hurry from my side. Thanks for supporting this effort.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2025-01-22 09:30:43 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2606716872  

What's the "nearest ancestor" problem referred to in the documentation for the fast Gabow algorithm? Is that LCA or something else?

---

## Comment 6

> Username: jorisvr  
> Created_at: 2025-01-22 20:20:20 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2608186808  

> What's the "nearest ancestor" problem referred to in the documentation for the fast Gabow algorithm?  
  
To be honest, I don't know. I kept this comment from the documentation by Yi Ji as I saw no reason to remove it.  
  
I know about the existence of that fast algorithm. I tried to read the paper by Gabow but I can not make heads or tails of it. Mehlhorn and Schaefer made an offhand remark that this algorithm may be unpractical (https://dl.acm.org/doi/10.1145/944618.944622 page 7) but that was a long time ago. I'm not aware of any public available implementation.

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2025-01-24 08:49:39 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2611984566  

Doesn't surprise me too much. Bender et. al. made a similar remark about the theoretically optimal algorithm for LCA: it just ain't worth it.

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2025-01-25 22:14:56 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2614116572  

Citation LCA remark: https://www.sciencedirect.com/science/article/abs/pii/S0196677405000854

---

## Review 9 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2025-01-25 23:13:37 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/400#pullrequestreview-2566668012  

I haven't even got to the code proper yet but here are a few requests and questions to start with.

📁 test/weighted_matching_test2.cpp

```diff
  15 |+ #include <sstream>
  16 |+ #include <type_traits>
  17 |+ #include <vector>
```

> Username: jeremy-murphy  
> Created_at: 2025-01-22 09:33:12 UTC  
> Updated_at: 2025-01-25 23:13:37 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1924994822  

Standard libraries go last. Sorry, one day I'll get clang-format to do it automatically.

> Username: jorisvr  
> Created_at: 2025-01-26 12:50:14 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1929765516  

Fixed. Thanks.


📁 doc/maximum_weighted_matching.html

```diff
  22 | void maximum_weighted_matching(const Graph&amp; g, MateMap mate, VertexIndexMap vm);
  23 | 
  24 |+ template &lt;typename Graph, typename MateMap, typename VertexIndexMap, typename EdgeWeightMap, bool Verify = true&gt;
```

> Username: jeremy-murphy  
> Created_at: 2025-01-25 23:01:53 UTC  
> Updated_at: 2025-01-25 23:13:37 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1929623176  

Leave the Verify parameter out, it's not worth complicating the interface for. If a user really wants to verify every result, they can write a short wrapper for themselves.

> Username: jorisvr  
> Created_at: 2025-01-26 12:28:49 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1929759925  

You are asking to remove `Verify` as parameter, and just never verify the result when called through this function. Right?  
  
Note that verification requires access to the blossom structure and dual variables, which are not returned by this function. So a user who wants to verify, will have to write their own interface function that reaches into `boost::graph::detail::maximum_weighted_matching_context`.  
  
At that point, should we consider to just remove the verification code entirely?  
I'm fine with removing it. I personally think that always verifying has merits, but I agree it is a trade-off against complication of the code.

> Username: jeremy-murphy  
> Created_at: 2025-01-29 09:40:33 UTC  
> Updated_at: 2025-01-29 09:40:34 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1933551808  

Yes, I think if we're not confident that the algorithm is reliable then we just have to test it more until we are. Releasing it with an option to check that the result is as expected just kind of screams of a lack of quality.  
On principle, we should return these blooms and other things anyway as a tuple of values that the user can just ignore if they're not interested in them. That also makes it simple for us to do this verification when we need to, without having to modify the function. (Which will be the case any time we make a change to the logic.)  
Now, having said that, I hope you don't have to make too many changes to the design just so that you can return these internals.

> Username: jeremy-murphy  
> Created_at: 2025-01-29 09:44:29 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1933557595  

I'm not 100% sure yet, but the verification code might not belong in the Boost.Graph library itself, but it would definitely belong in something that _fuzzes_ it. That does exist elsewhere, so that could be a next step after getting this PR finished.

> Username: jorisvr  
> Created_at: 2025-01-29 18:34:44 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1934391212  

Ok. It seems clear that we are not going to verify answers by default, and I agree with your reasoning for that decision.  
  
I would then recommend to just remove the verification routine. Normal users are not going to use it. The verification logic itself is not trivial. Keeping such unused code in the library will just be a burden.  
  
Putting the verification code in a fuzzer is interesting. I guess the fuzzer can just directly access the internal class to reach the data it needs. Alternatively, the fuzzer can compare answers against a trusted 3rd party implementation like LEMON.  
  
Returning the blossom structure and duals is possible, but these data then need to be rearranged into a suitable structure at the end of the algorithm. During the algorithm, I need them in an efficient form. Returning this seems like more trouble than it is worth. It would also expose internal details about the algorithm, possibly complicating future revisions.  
  
If you agree, I will add a commit to drop the verification stuff.

> Username: jeremy-murphy  
> Created_at: 2025-01-29 22:54:18 UTC  
> Updated_at: 2025-01-29 22:54:19 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1934764984  

Yeah, let's focus on the primary goal: fixing the implementation. We can always add more or tweak it later. So yes, for now let's remove the verification code altogether and don't worry about returning the blossom structures.

> Username: jorisvr  
> Created_at: 2025-01-31 07:31:53 UTC  
> Updated_at: 2025-01-31 07:31:54 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1936792327  

Done.

---

📁 doc/maximum_weighted_matching.html

```diff
 105 |- because the algorithm maintains blossoms, dual variables and node labels across all augmentations.
 103 |+ <p>
 104 |+ The implementation deviates from the description in [<a href="bibliography.html#galil86">77</a>] on a few points:
```

> Username: jeremy-murphy  
> Created_at: 2025-01-25 23:08:33 UTC  
> Updated_at: 2025-01-25 23:13:37 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1929623977  

I think you need to provide more explanation and evidence about why these deviations from an algorithm published in a refereed journal are good. Even better would be to get your deviations published in a journal. :)

> Username: jorisvr  
> Created_at: 2025-01-26 12:41:28 UTC  
> Updated_at: 2025-01-26 12:41:29 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1929763847  

I see your point, but I'd like to understand how you want me to solve it.  
  
The text already describes the benefit of each deviation. If you just want me to elaborate, I can e.g. expand each bullet into a full paragraph, provide some details, etc.  
  
The reference paper has a focus on time complexity, not practical efficiency. From a complexity point of view, these deviations are trivial tinkering since the time complexity remains the same. If you want, I can provide specific arguments to point this out.  
  
Reusing labels is not my original idea. It is widely used, for example Mehlhorn and Schäfer [1], LEMON, Kolmogorov [2]. But they all use different base algorithms, O(V E log(V)) and O(V^2 E) respectively, therefore the performance trade-off is different. I did some informal benchmarking on random graphs, but printing benchmarks in the documentation seems out-of-place to me.  
  
[1] https://dl.acm.org/doi/abs/10.1145/944618.944622  
[2] https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=930b9f9c3bc7acf3277dd2361076d40ff03774b2

> Username: jorisvr  
> Created_at: 2025-01-31 07:35:08 UTC  
> Updated_at: 2025-01-31 07:35:09 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1936796030  

I expanded the text a bit to argue explicitly that each change preserves the overall time complexity and correctness of the algorithm.

> Username: jeremy-murphy  
> Created_at: 2025-02-05 10:59:58 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1942663544  

Thanks. I'm still not hugely comfortable with unpublished changes to a published algorithm, but I don't want to be rude and throw out your valuable contribution.   
Since after all it fixes the known bugs, I think the pragmatic thing to do is accept it as is and let the community figure out if it's right. :)

---

📁 doc/maximum_weighted_matching.html

```diff
 179 |+ <tt>VertexIndexMap</tt> and <tt>EdgeWeightMap</tt> both provide constant-time lookup, the time complexity for
 180 |+ <tt>maximum_weighted_matching</tt> is <i>O(n<sup>3</sup>)</i>.
 181 |+ For <tt>brute_force_maximum_weighted_matching</tt>, the time complexity is exponential of <i>m</i>.
```

> Username: jeremy-murphy  
> Created_at: 2025-01-25 23:11:14 UTC  
> Updated_at: 2025-01-25 23:13:37 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1929624349  

I could be mistaken, but I think the correct phrasing is "exponential _in_" rather than "exponential _of_".

> Username: jorisvr  
> Created_at: 2025-01-26 12:49:59 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1929765478  

Agreed. Fixed.


📁 test/Jamfile.v2

```diff
 115 |     [ run matching_test.cpp ]
 116 |     [ run weighted_matching_test.cpp ]
 117 |+     [ run weighted_matching_test2.cpp ]
```

> Username: jeremy-murphy  
> Created_at: 2025-01-25 23:12:21 UTC  
> Updated_at: 2025-01-25 23:13:37 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1929624538  

Does it need to be a second file? Just wondering why.

> Username: jorisvr  
> Created_at: 2025-01-26 12:48:53 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1929765287  

I was initially apprehensive about changing existing code and thought it best to keep the new stuff separate.  
  
The files can be merged. The styles are quite different though. `weighted_matching_test` reads testcases from a data file, while `test2` builds testcases in commented C++ code. I prefer to keep the coded style for the new test cases.  
  
Let me know if you want me to merge into a single test program.

> Username: jeremy-murphy  
> Created_at: 2025-01-29 23:00:51 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1934774401  

It's fine, leave them separate, but maybe rename their suffix to distinguish how they're different. It's not too important.


---

## Review 10 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:05:03 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/400#pullrequestreview-2595341903  

Sorry, just a trivial request, but since the library is C++14 now can you please use `using` instead of `typedef` and remove spaces from in-between nested template <<....>> brackets? Thanks. I will enable clang-format one day...  
PS. I mean, a template should be written as  
`template <typename Foo>`, etc.

---

## Review 11 [Commented]

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:34:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/400#pullrequestreview-2595365331  

Not quite finished...

📁 test/weighted_matching_test2.cpp

```diff
 517 |+         run_random_graph< long >(10, 15, rng);
 518 |+     for (int k = 0; k < 10000; ++k)
 519 |+         run_random_graph< double >(10, 15, rng);
```

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:15:06 UTC  
> Updated_at: 2025-02-05 11:34:27 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1942683426  

Please remove the random graphs, that should be moved to fuzz testing. Unit tests should specifically exercise different paths through the algorithm and edge cases, with as few tests as possible.

> Username: jorisvr  
> Created_at: 2025-02-05 21:00:19 UTC  
> Updated_at: 2025-02-05 21:00:20 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943655942  

Ok, done.


📁 include/boost/graph/maximum_weighted_matching.hpp

```diff
  69 | template < typename Graph, typename MateMap, typename VertexIndexMap >
  53 |- class weighted_augmenting_path_finder
  70 |+ class brute_force_matching
```

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:19:08 UTC  
> Updated_at: 2025-02-05 11:34:27 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1942688515  

I think we need to be specific about which algorithm this is, since everything is in the same namespace. Also, maybe _matcher_ is better than _matching_?

> Username: jorisvr  
> Created_at: 2025-02-05 21:03:11 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943658991  

I kept this piece of code unchanged from the previous version. But I'm happy to change it. Shall I make it `class brute_force_maximum_weighted_matcher` ?

> Username: jeremy-murphy  
> Created_at: 2025-02-05 22:44:50 UTC  
> Updated_at: 2025-02-05 23:05:42 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943778001  

Hmm, if that was the old name then it's fine to leave it for now. Sorry, because I'm reviewing on my phone, I can only see small bits of code at a time and so I often overlook these connections.

---

📁 include/boost/graph/maximum_weighted_matching.hpp

```diff
 106 |+ 
 107 |+ private:
 108 |+     const Graph& g;
```

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:22:10 UTC  
> Updated_at: 2025-02-05 11:34:27 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1942692406  

Add a general rule, please don't use references as member variables, as they prevent the class from being Regular. Uglier as it might be, just use a pointer.

> Username: jorisvr  
> Created_at: 2025-02-05 21:10:58 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943667181  

Is it useful for this implementation class to be Regular? The reference is not the only thing that makes it irregular, for example the class is not equality-comparable.  
  
The pattern with member variables `const Graph& g` is used in several places in the BGL.   
  
If you insist on a pointer, I will trust your judgement and make it into a pointer. I'd just like to make sure since I don't understand the reasons for it.

> Username: jeremy-murphy  
> Created_at: 2025-02-05 22:46:05 UTC  
> Updated_at: 2025-02-05 23:05:42 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943779304  

Let me have a closer look on a proper screen and I'll get back to you.

> Username: jeremy-murphy  
> Created_at: 2025-02-06 02:05:42 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943965766  

Yeah it's used in existing code, but it's a bad practice, so let's not add more.

> Username: jorisvr  
> Created_at: 2025-02-06 23:17:34 UTC  
> Updated_at: 2025-02-06 23:17:35 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1945604132  

Ok, done.

> Username: jeremy-murphy  
> Created_at: 2025-02-24 22:15:47 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1968492531  

Something recently reminded me that there is a better way to do this than using a raw pointer, which is to use `std::reference_wrapper`.   
https://en.cppreference.com/w/cpp/utility/functional/reference_wrapper  
It might not be a perfect solution but I would definitely have a go at using it to see if it improves readability.

> Username: jorisvr  
> Created_at: 2025-02-25 22:09:06 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1970612204  

> `std::reference_wrapper`  
  
I did look into that, but I could not get it to work in a way that seems helpful.  
In particular, the following does not work:  
  
```  
std::reference_wrapper<const Graph> g;  
  
...  
auto nv = num_vertices(g);  
```  
  
If I write that, I get a type error. Although `std::reference_wrapper` is supposedly implicitly convertable to the stored reference type, that doesn't seem to work in this case. Perhaps because `num_vertices()` is a function template, the reference wrapper itself ends up being passed as function argument.  
  
I _can_ write `num_vertices(g.get())`  
but I'm not sure it is much of an improvement over `num_vertices(*g)`.

> Username: jeremy-murphy  
> Created_at: 2025-02-26 09:21:02 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1971226788  

Ahhh, yeah, it doesn't magically remove in that case. Oh well, too bad.

---

📁 include/boost/graph/maximum_weighted_matching.hpp

```diff
 171 |+     typedef typename std::make_unsigned<index_t>::type unsigned_index_t;
 172 |+     auto nv = num_vertices(g);
 173 |+     std::vector<bool> got_vertex(nv);
```

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:29:26 UTC  
> Updated_at: 2025-02-05 11:34:27 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1942702041  

Make sure this is really what you want, as opposed to a bitset.

> Username: jorisvr  
> Created_at: 2025-02-05 21:12:47 UTC  
> Updated_at: 2025-02-05 21:12:48 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943668932  

I believe `std::bitset<N>` requires its size to be fixed at compile time. But I need an array which is sized at run time to match the number of vertices of the graph.

> Username: jeremy-murphy  
> Created_at: 2025-02-05 22:47:44 UTC  
> Updated_at: 2025-02-05 23:05:42 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943780793  

Yes, sorry, I meant Boost's `dynamic_bitset`.

> Username: jorisvr  
> Created_at: 2025-02-06 23:21:19 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1945606964  

Ah ok. `vector<bool>` and `dynamic_bitset` both provide the functionality I need. I don't see a specific reason to prefer one or the other.

> Username: jeremy-murphy  
> Created_at: 2025-02-26 09:20:22 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1971225693  

I forgot to address this one, whoops.   
`vector<bool>` is generally avoided because of its unusual performance trying to satisfy the standard container interface (providing a reference to each element via iteration) whilst only using one bit per value, which requires the use of a proxy class, etc.   
A dynamic bitset is just more honest about what it is and does.  
It's not a big deal, might not need to be changed until someone makes some other changes in there.

---

📁 include/boost/graph/maximum_weighted_matching.hpp

```diff
 217 |+      * direction of the path.
 218 |+      */
 219 |+     typedef std::deque< vertex_pair_t > alternating_path_t;
```

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:30:50 UTC  
> Updated_at: 2025-02-05 11:34:27 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1942703734  

deque is rarely faster than vector in practice: did you benchmark?

> Username: jorisvr  
> Created_at: 2025-02-05 21:15:01 UTC  
> Updated_at: 2025-02-05 21:15:02 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943671264  

I add elements on both sides of the deque while tracing the path in two directions. To do that with a vector requires some extra tricks.

---

📁 include/boost/graph/maximum_weighted_matching.hpp

```diff
 299 |+ 
 300 |+         /** List of sub-blossoms, ordered along the alternating cycle. */
 301 |+         std::list< sub_blossom_t > subblossoms;
```

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:31:49 UTC  
> Updated_at: 2025-02-05 11:34:27 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1942704994  

And list is typically much much worse than vector; did you benchmark?

> Username: jorisvr  
> Created_at: 2025-02-05 21:19:48 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943676187  

I had not benchmarked, but I have now and there is no measurable difference. This list is not accessed very often.  
  
I chose list because I use `splice()` to reorient the base of the list during augmentation. It is just a matter of convenience. I can instead use vector and either extra bookkeeping or copying of elements.  
  
Do you prefer `std::vector` over `std::list` in cases where the performance is neutral?

> Username: jeremy-murphy  
> Created_at: 2025-02-05 22:49:14 UTC  
> Updated_at: 2025-02-05 23:05:42 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943781955  

I didn't notice the splice, sorry, proceed as is.

---

📁 include/boost/graph/maximum_weighted_matching.hpp

```diff
 477 |+     static void for_vertices_in_blossom(const blossom_t* blossom, Func func)
 478 |+     {
 479 |+         const nontrivial_blossom_t* ntb = blossom->nontrivial();
```

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:33:53 UTC  
> Updated_at: 2025-02-05 11:34:27 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1942707671  

Just use auto?

> Username: jorisvr  
> Created_at: 2025-02-05 21:21:40 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943677973  

I don't understand.  
Do you mean `auto func` instead of `template <typename Func>` ? That was not allowed before C++20.  
  
Or do you mean `auto ntb` instead of `const nontrivial_blossom_t* ntb` ?

> Username: jeremy-murphy  
> Created_at: 2025-02-05 22:49:56 UTC  
> Updated_at: 2025-02-05 23:05:42 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943782520  

The latter.

> Username: jorisvr  
> Created_at: 2025-02-06 23:25:16 UTC  
> Updated_at: 2025-02-06 23:25:17 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1945609845  

Ok. I changed it to `auto` for these and a few similar verbose declarations.  
  
Is that what you had in mind, or do you want to push further towards the *almost-alway-auto* style?

> Username: jeremy-murphy  
> Created_at: 2025-02-10 02:24:20 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1948324254  

I personally use and recommend AAA style, but what you've done here is fine. I just felt those long typenames with qualifiers were not helping the readability.


---

## Review 12 [Commented]

> Username: jeremy-murphy  
> Created_at: 2025-02-05 23:05:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/400#pullrequestreview-2595426273  

Still going,..

📁 include/boost/graph/maximum_weighted_matching.hpp

```diff
 245 |+ 
 246 |+         /** True if this is an instance of nontrivial_blossom. */
 247 |+         const bool is_nontrivial_blossom;
```

> Username: jeremy-murphy  
> Created_at: 2025-02-05 11:42:45 UTC  
> Updated_at: 2025-02-05 23:05:42 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1942718850  

No const member variables either, same reason as references, but I'll review again on a proper screen to decide if it's worth changing.

> Username: jeremy-murphy  
> Created_at: 2025-02-06 02:03:46 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943964153  

It's a good general principle, so yeah, please change it.

> Username: jorisvr  
> Created_at: 2025-02-06 23:25:33 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1945610073  

Done.

---

📁 include/boost/graph/maximum_weighted_matching.hpp

```diff
 422 |-             for (vertex_vec_iter_t v = sub_vertices.begin();
 423 |-                  v != sub_vertices.end(); ++v)
 393 |+             if ((! edge.has_value()) || (s < slack))
```

> Username: jeremy-murphy  
> Created_at: 2025-02-05 22:56:53 UTC  
> Updated_at: 2025-02-05 23:05:42 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1943788385  

I'm personally not in favour of a space between a unary operator such as `!` and its operand, same as for other unary operators like dereference `*`, negation `-`, etc.   
I don't think BGL has a history of it, but maybe I'm wrong?

> Username: jorisvr  
> Created_at: 2025-02-06 23:31:25 UTC  
> Url: https://github.com/boostorg/graph/pull/400#discussion_r1945614073  

Oh, I probably came up with this. I don't see it anywhere else in BGL.  
I removed the spaces now.  
  
I really like the space, but this is not the time to start a discussion about code formatting.


---

## Comment 13

> Username: jeremy-murphy  
> Created_at: 2025-02-06 01:52:12 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2638518673  

There are some functions that take a `blossom*` but require that it is not null, in which case they should take a reference instead.

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2025-02-06 02:02:26 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2638572311  

That's all from me, once the last comments are resolved I'll merge it in.  
Overall I love the code, so thank you!

---

## Comment 15

> Username: jorisvr  
> Created_at: 2025-02-06 23:39:44 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2641358692  

> There are some functions that take a `blossom*` but require that it is not null, in which case they should take a reference instead.  
  
I now changed these into `blossom&`.  
This involved adding a number of `*` and `&` operators in the code. Because `blossom*` is still the native type in several data structures, I end up going back-and-forth between pointer and reference. In my opinion, it makes the code less clear. But I can live with it.

---

## Comment 16

> Username: jeremy-murphy  
> Created_at: 2025-02-10 02:16:28 UTC  
> Updated_at: 2025-02-10 02:18:18 UTC  
> Url: https://github.com/boostorg/graph/pull/400#issuecomment-2646773590  

> > There are some functions that take a `blossom*` but require that it is not null, in which case they should take a reference instead.  
>   
> I now changed these into `blossom&`. This involved adding a number of `*` and `&` operators in the code. Because `blossom*` is still the native type in several data structures, I end up going back-and-forth between pointer and reference. In my opinion, it makes the code less clear. But I can live with it.  
  
I know what you mean, sometimes you just have to pay a price for doing things correctly, but it also might mean that those other places using `blossom*` could do with some refactoring.   
  
PS. And I realize that sounds hypocritical because I asked you to change the member variables from reference to pointer. Sometimes C++ is just ugly.

---
