# #110 O(n^3) maximum weighted matching [Merged]

> Username: yi-ji  
> Created at: 2018-06-24 14:39:46 UTC  
> Updated at: 2019-01-23 19:36:58 UTC  
> Merged at: 2019-01-23 19:35:05 UTC  
> Closed at: 2019-01-23 19:35:05 UTC  
> Url: https://github.com/boostorg/graph/pull/110  

This pull request contains:  
  
- `include/boost/graph/maximum_weighted_matching.hpp`  
 An O(|V|^3) implementation of maximum weighted matching algorithm for general graphs, and a verifier using brute-force searching.  
  
- `test/weighted_matching_test.cpp`  
Tests on randomly generated graphs using the brute-force verifier.  
  
- `example/weighted_matching_example.cpp`  
Some simple usage examples.  
  
The O(|V|^3) implementation refers to:  
Eugene L. Lawler, _Combinatorial Optimization: Networks and Matroids_, Chapter 6.10  
(Note that there are a few mistakes in the book.)  
  
Thanks to @aaw for his good implementation of maximum cardinality matching that helped me clear my mind when starting.

---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 17:32:08 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-417736922  

I'm helping out with the PR backlog. Looks like you have a feature, test, and a new example. This will be in the second batch of merged PR's. This is to let you know and help me prioritize PR's.

---

## Comment 2

> Username: yi-ji  
> Created_at: 2018-09-03 05:55:12 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-418007584  

@anadon Great news, really thank you for what you are doing!  
Please let me know if you need anything :)

---

## Comment 3

> Username: anadon  
> Created_at: 2018-10-12 19:54:21 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-429444242  

@yi-ji Please rebase your PR on the current devel.  There are some CI tools which have been enabled and a number of important cleanups.

---

## Review 4 [Commented]

> Username: anadon  
> Created_at: 2018-10-14 01:13:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/110#pullrequestreview-164481136  

📁 test/weighted_matching_test.cpp

```diff
 110 |     }
 111 |+ 
 112 |+     //print_graph(g);
```

> Username: anadon  
> Created_at: 2018-10-14 01:13:20 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976160  

Should this be removed?

> Username: yi-ji  
> Created_at: 2018-10-14 01:28:28 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976343  

Yes, sorry didn't notice, will do


---

## Review 5 [Commented]

> Username: anadon  
> Created_at: 2018-10-14 01:13:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/110#pullrequestreview-164481143  

📁 test/weighted_matching_test.cpp

```diff
 151 |-     std::size_t batch_size = 100;
 152 |+     std::size_t max_num_e = 24;
 153 |+     std::size_t batch_size = 16;
```

> Username: anadon  
> Created_at: 2018-10-14 01:13:49 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976166  

How do you calculate the optimal test size?

> Username: yi-ji  
> Created_at: 2018-10-14 01:33:58 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976409  

I didn't, because I am using a brute-force verifier, it can be time-consuming when edge num increases (exponential), and travis-ci has time limit

> Username: anadon  
> Created_at: 2018-10-14 01:36:57 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976451  

Why not optimize it locally first?

> Username: yi-ji  
> Created_at: 2018-10-14 01:39:09 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976475  

I can wait for as long as it takes on my machine, but I didn't know about travis-ci time limit

> Username: anadon  
> Created_at: 2018-10-14 01:41:20 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976507  

I'm sure we can increase the time limit if it increases correctness.  But you should be able to determine the rough upper and lower bounds of these variables for testing correctness.

> Username: yi-ji  
> Created_at: 2018-10-14 01:48:34 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976573  

You're right, I will give a rough time estimate regarding edge num


---

## Review 6 [Commented]

> Username: anadon  
> Created_at: 2018-10-14 01:14:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/110#pullrequestreview-164481154  

📁 include/boost/graph/maximum_weighted_matching.hpp

```diff
  56 |         };
  57 |-         typedef decltype(graph::detail::V_EVEN) vertex_state_t; // anonymous enum defined elsewhere
  57 |+         typedef unsigned short vertex_state_t;
```

> Username: anadon  
> Created_at: 2018-10-14 01:14:36 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976174  

Does this type ever change in graph?

> Username: yi-ji  
> Created_at: 2018-10-14 01:29:30 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976358  

It's defined as anonymous enum and used only in `graph/max_cardinality_matching.hpp` except here,  
see: https://github.com/boostorg/graph/blob/develop/include/boost/graph/max_cardinality_matching.hpp#L29   
I am making this change because old clang and g++ doesn't support typedef decltype from unnamed enum

> Username: anadon  
> Created_at: 2018-10-14 01:31:32 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976373  

Then could it be removed entirely and not just its use here?

> Username: yi-ji  
> Created_at: 2018-10-14 01:43:34 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976518  

"Here" is `graph/maximum_weighted_matching.hpp`, this enum is defined (and used) in `graph/max_cardinality_matching.hpp`, not by me.   
Another idea is to assign a name for this enum in `graph/max_cardinality_matching.hpp` but I think maybe it's better not to tamper with existed files

> Username: anadon  
> Created_at: 2018-10-14 01:45:57 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976536  

I'm a little uncomfortable with changing something which seems to be related to a template parameter.  If this is just for testing time, it is probably best to leave it be.

> Username: yi-ji  
> Created_at: 2018-10-14 01:54:46 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224976647  

This is actually not for testing but they way it's supposed to be I think..  
Did you mean leave `decltype(graph::detail::V_EVEN)` be? it won't compile by g++  
I understand you concern, however `vertex_state_t` has only possible values of {0, 1, 2} (no chance to be changed by logic) and only used in `graph/maximum_weighted_matching.hpp`, so unsigned short shouldn't cause any problem I think

> Username: anadon  
> Created_at: 2018-10-14 03:51:05 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224978684  

OK.  This is new code so good enough for me at this point.  I'll read through the PR in more detail when the tests pass.

> Username: yi-ji  
> Created_at: 2018-10-15 04:23:28 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r225035095  

It seems anonymous enum can be troublesome when involved with template parameter, so I assigned a name `VERTEX_STATE` to the unnamed enum in `graph/max_cardinality_matching.hpp`. I don't like changing existed files but this seems to be the most proper solution.  
 I will later push again after I change random tests to pre-computed answers comparison.


---

## Review 7 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-10-14 02:38:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/110#pullrequestreview-164482314  

📁 test/weighted_matching_test.cpp

```diff
  18 |+ #include <ctime>
  19 |+ #include <boost/random.hpp>
  20 |+ #include <boost/test/minimal.hpp>
```

> Username: jeremy-murphy  
> Created_at: 2018-10-14 02:38:31 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224977172  

I strongly recommend that you don't use randomly generated test cases here. Using them as a separate utility to discover bugs is up to you, but the test cases here should attempt to provide good coverage of the algorithm by providing inputs that execute different code paths, edge cases, etc.  
Then you also don't need to run a brute-force verification: just store the answer and compare.

> Username: yi-ji  
> Created_at: 2018-10-14 02:48:47 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r224977333  

Nice advice, thank you. I will remove brute-force verification and use pre-computed answers instead.


---

## Comment 8

> Username: anadon  
> Created_at: 2018-10-15 03:50:03 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-429700396  

It looks like the anonymous enum to short is causing some other knock off effects causing compilation failure.

---

## Comment 9

> Username: yi-ji  
> Created_at: 2018-10-21 02:58:51 UTC  
> Updated_at: 2018-10-26 13:57:03 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-431634780  

@anadon Not sure why jobs [87.17](https://travis-ci.org/boostorg/graph/jobs/444129829) and [87.18](https://travis-ci.org/boostorg/graph/jobs/444129830) failed because no output shown...seems related to clang++5.0? Though I tested locally without seeing anything wrong  
____________  
A re-run (without any code change) has passed!

---

## Comment 10

> Username: anadon  
> Created_at: 2018-11-01 14:02:52 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-435050035  

@jzmaddock This looks good to me to merge.  It provides an interesting addition to BGL.

---

## Comment 11

> Username: anadon  
> Created_at: 2018-11-01 14:03:30 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-435050196  

@yi-ji Make sure to add documentation and examples.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2018-11-01 18:14:41 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-435134621  

We have an example, but we do need docs before merging.  Please add "[CI SKIP]" to pure documentation commit messages to stop CI builds cycling needlessly: we're short on CI testing time especially on appveyor which seems to all run through my account :(

---

## Comment 13

> Username: anadon  
> Created_at: 2018-11-01 19:31:53 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-435158734  

Would it help if I set up something through appveyor to run these through?

---

## Comment 14

> Username: yi-ji  
> Created_at: 2018-11-15 16:16:09 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-439097113  

@anadon @jzmaddock documentation added :)

---

## Comment 15

> Username: anadon  
> Created_at: 2018-11-19 02:47:44 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-439755940  

I'm not seeing CI run.  Do you have an idea of what is up with that?

---

## Comment 16

> Username: yi-ji  
> Created_at: 2018-11-19 02:54:50 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-439756923  

@anadon  I added "[CI SKIP]" into the commit message as @jzmaddock [asked](https://github.com/boostorg/graph/pull/110#issuecomment-435134621). No need to run a CI build for pure document commits, right?

---

## Comment 17

> Username: anadon  
> Created_at: 2018-11-19 04:23:18 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-439767782  

That's right, sorry.  
  
On Sun, Nov 18, 2018 at 9:54 PM yi-ji <notifications@github.com> wrote:  
  
> @anadon <https://github.com/anadon> I added "[CI SKIP]" into the commit  
> message as @jzmaddock <https://github.com/jzmaddock> asked  
> <https://github.com/boostorg/graph/pull/110#issuecomment-435134621>. No  
> need to run a CI build for pure document commits, right?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/110#issuecomment-439756923>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AC-CBX601zw9Rf6wrynhIKaelkuZTFgIks5uwh16gaJpZM4U1LRA>  
> .  
>

---

## Comment 18

> Username: yi-ji  
> Created_at: 2018-12-14 10:44:24 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-447287494  

@jzmaddock Could you help merge this PR?

---

## Comment 19

> Username: jeremy-murphy  
> Created_at: 2018-12-14 12:09:52 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-447306741  

If you all don't mind, I would like to have a good look through the algorithm implementation before we merge. There is a lot of code there.  
I see some use of `shared_ptr` and virtual functions that I'm a bit suspicious about.  
  
And someone remind me... is Boost.Graph still on a strict C++03 diet?

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2018-12-14 17:56:02 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-447402638  

>If you all don't mind, I would like to have a good look through the algorithm implementation before we merge. There is a lot of code there.  
>I see some use of shared_ptr and virtual functions that I'm a bit suspicious about.  
  
It would be great if someone would give this a once over.  
  
>And someone remind me... is Boost.Graph still on a strict C++03 diet?  
  
I'd prefer to keep existing code passing with existing compilers.  But for strict additions, I don't see why we can't use C++20 if that's the only way to do it.  As long as it's documented of course.  In any case this looks all C++03 to me at first glance, and the few things that have C++11 equivalents (shared_ptr, tie) are internal details so far as I can see.

---

## Review 21 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-12-15 02:10:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/110#pullrequestreview-185338086  

📁 doc/bibliography.html

```diff
 448 |+ <em>Data Structures for Weighted Matching and Nearest Common Ancestors with Linking</em><br>
 449 |+ Proceedings of the First Annual ACM-SIAM Symposium on Discrete Algorithms, pp. 434-443, 1990.
 450 |+ 
```

> Username: jeremy-murphy  
> Created_at: 2018-12-15 02:10:29 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r241934090  

I know this publication from 1990 is more recent, but maybe his original paper on the implementation is more relevant and specific? https://dl.acm.org/citation.cfm?id=321942

> Username: yi-ji  
> Created_at: 2018-12-18 15:53:59 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r242593452  

I agreed, change will be made.   
Just to be clear, the labeling method is not generalized to weighted matching in the paper you mentioned, though it does can be.   
I have to admit that the 1990 paper focused more about improving the time complexity from O(n^3) to O(n(m+nlogn)) by proposing link-cut-trees to find nearest common ancestor, which is not implemented in this PR..

> Username: jeremy-murphy  
> Created_at: 2018-12-19 10:52:00 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r242872058  

I'm not quite sure what you mean about the labeling method is not generalized in that paper?  
  
But otherwise, glad my suggestion was helpful.  
  
I didn't realize that NCA (which I think is typically called LCA?) is part of this algorithm, although I haven't finished going through it yet. Where is it in here?

> Username: yi-ji  
> Created_at: 2018-12-19 14:16:38 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r242930656  

I mean, the labeling procedure in the paper you mentioned only applies to unweighted matching. The author just claimed at the end that it could be generalized to weighted matching.  
  
Link-cut-tree based NCA/LCA is not implemented in this PR. I am using a trivial method, which is used in `max_cardinality_matching.hpp` as well.  
  
In fact, this 1990 citation should not be deleted... I didn’t look carefully where it is cited when replying you. It’s not cited for algorithm reference, but for pointing out the best known time complexity is not O(n^3).  
I cited Edmond: “Maximum Matching and a Polyhedron with 0, 1-Vertices” for my implementation.   
Of course I will still add the paper you mentioned, because Edmond’s paper did not elaborate implementation detail for labeling procedure.


---

## Review 22 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-12-15 10:19:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/110#pullrequestreview-185350133  

📁 doc/maximum_weighted_matching.html

```diff
  37 |+ and its weight sum can't be increased by adding edges.
  38 |+ The matching on the right is a maximum weighted matching of size 7 and weight sum 38, meaning that it has maximum
  39 |+ weight sum over all matchings in the graph.
```

> Username: jeremy-murphy  
> Created_at: 2018-12-15 10:19:51 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r241945044  

In terms of nomenclature, Gabow (1990) mentions a _maximum weight matching_ problem but in his earlier 1976 paper the problem is called _maximum matching_, as it is by Edmonds in his original paper.  
My guess is that he added "weight" in to differentiate against the _maximum cardinality matching_ problem.  
But either way, I'm going to suggest that it is "weight", not "weighted", although I know he uses the latter in some other names. Anyway, it's not a serious issue, just my pedantic opinion.  
  
In addition to that, the 1990 paper raises another point, that this O(n^3) algorithm is not the only algorithm that solves maximum weight matching, as it presents an O(n(m + n log n)) algorithm. It's perfectly reasonable to implement the O(n^3) algorithm, but we need to name it such that we can also name the newer algorithm, e.g.:  
  
- `brute_force_maximum_weight_matching` -- O(?)  
- `label_encoded_maximum_weight_matching` -- O(n^3)  
- `blossom_expansion_maximum_weight_matching` -- O(n(m + n log n))  
  
They are just ideas, I'm not sure if they are entirely accurate, especially the last one.  
  
Then, maybe, we could have a `maximum_weight_matching` that uses some heuristics to pick the fastest algorithm for a given problem size. Just an idea, not important.

> Username: yi-ji  
> Created_at: 2018-12-18 16:16:30 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r242602975  

I believe both "maximum weight / weighted matching" are commonly used. In fact, another large C++ template library for network algos, [LEMON](http://lemon.cs.elte.hu/pub/doc/latest/a00254.html), uses `MaxWeightedMatching`.  
  
Thanks for the name prefix suggestion. However the examples might not be quite accurate. The O(n^3) and O(n(m + n log n)) algorithms both have labeling and blossom expansion operations. The only difference is the O(n(m + n log n)) algorithms uses link-cut-tree to find nearest common ancestors, which is not important/radical/obvious enough to come up with different names.  
I didn't implement the O(n(m + n log n)) algo mainly because there is no link-cut-tree module in boost. If it becomes available and someone wants to improve to O(n(m + n log n)) someday, he/she could just make a few lines of code change and doesn't have to keep an O(n^3) version. By then, a new templete parameter for controlling whether to use link-cut-tree would be more reasonable.

> Username: jeremy-murphy  
> Created_at: 2018-12-19 11:00:51 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r242874884  

Ah, hmmm, OK, you think the difference between the O(n^3) and O(n(m + n log n)) algorithms will be a matter of passing in different LCA sub-algorithms? That would certainly make the naming easier.  
  
Funnily enough, I am actually working on... close to finishing... a pragmatic implementation of LCA, but it's not Gabow's  link-cut version.  When I have a chance, I'll have a look at how much more work it would be to extend it, although I want to finish the basic implementation first.

> Username: yi-ji  
> Created_at: 2018-12-19 14:20:17 UTC  
> Updated_at: 2018-12-22 16:33:28 UTC  
> Url: https://github.com/boostorg/graph/pull/110#discussion_r242931945  

Thanks. More funnily... I am also working on and close to finishing a link-cut-tree module😂 but I believe it’s okay if we open dIfferent PR. Let’s see what to do then.


---

## Comment 23

> Username: yi-ji  
> Created_at: 2018-12-22 16:36:42 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-449582350  

Review comments resolved. Ready for merge again! (If no more of them)

---

## Comment 24

> Username: jeremy-murphy  
> Created_at: 2018-12-23 21:05:23 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-449663194  

I haven't had a chance to look at the code itself in detail yet and realistically I won't until the new year. I'm inclined to say that so long as it is well tested (which I think it is), let's not let perfect be the enemy of good, merge it now, and look at refactoring when we have time.

---

## Comment 25

> Username: yi-ji  
> Created_at: 2019-01-23 16:02:51 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-456859071  

Happy 2019 guys.. so is there anything stalling the merge...?

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2019-01-23 19:36:58 UTC  
> Url: https://github.com/boostorg/graph/pull/110#issuecomment-456937262  

Merged.  
I've re-ordered the initialization of members of maximum_weighted_matching to avoid pages and pages of gcc warnings.  Also added an entry to the documentation table-of-contents.

---
