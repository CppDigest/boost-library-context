# #269 Revert change to `reindex_edge_list`. [Merged]

> Username: cqc-alec  
> Created at: 2021-07-02 13:21:09 UTC  
> Updated at: 2022-02-21 00:44:35 UTC  
> Merged at: 2022-02-21 00:43:28 UTC  
> Closed at: 2022-02-21 00:43:28 UTC  
> Url: https://github.com/boostorg/graph/pull/269  

Fixes #268  .

---

## Review 1 [Commented]

> Username: prince-chrismc  
> Created_at: 2021-07-22 23:35:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/269#pullrequestreview-713310846  

You should be able to keep the for loop and remove the increments if you correctly update after the erease

📁 include/boost/graph/detail/adjacency_list.hpp

```diff
2077 |                 typename EdgeList::value_type ce = *ei;
2078 |+                 ++ei;
2079 |                 el.erase(ce);
```

> Username: prince-chrismc  
> Created_at: 2021-07-22 23:33:38 UTC  
> Updated_at: 2021-07-22 23:35:03 UTC  
> Url: https://github.com/boostorg/graph/pull/269#discussion_r675225922  

```diff  
+                ei = el.erase(ce);  
```

> Username: cqc-alec  
> Created_at: 2021-07-23 10:05:37 UTC  
> Url: https://github.com/boostorg/graph/pull/269#discussion_r675453741  

Thanks for the suggestion. It indeed looks like a better approach; however, I was not able to get this code to compile:  
  
```  
#include <boost/graph/adjacency_list.hpp>  
  
using Graph = boost::adjacency_list<boost::setS, boost::vecS, boost::undirectedS>;  
  
int main() {  
  Graph g(3);  
  boost::add_edge(2, 0, g);  
  boost::remove_vertex(1, g);  
}  
```  
  
The gist of the error is that `erase` does not return an iterator in this case, but a `long unsigned int`.  
  
(The original patch was simply a reversion of a previous commit.)


---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2021-12-09 05:57:06 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-989539229  

Could you please add the example from the bug report that can trigger this segfault as a new unit test?  
As long as it is reliable under valgrind or a memory sanitizer, that is sufficient.

---

## Comment 3

> Username: cqc-alec  
> Created_at: 2021-12-09 10:29:45 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-989721651  

> Could you please add the example from the bug report that can trigger this segfault as a new unit test? As long as it is reliable under valgrind or a memory sanitizer, that is sufficient.  
  
Yes, I will do.

---

## Comment 4

> Username: cqc-alec  
> Created_at: 2021-12-10 13:11:26 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-990961895  

I have added the unit test to `delete_edge.cpp`, which seems the most appropriate existing test file -- is that OK or would you prefer a new source file?

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2022-01-05 05:35:40 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1005396766  

> I have added the unit test to `delete_edge.cpp`, which seems the most appropriate existing test file -- is that OK or would you prefer a new source file?  
  
I think the location is fine.  
  
Is there a reason that you can't use the existing `m_graph` object? I know the MWE given uses undirected edges but the explanation does not stipulate it. Hmm. Anyway, it's probably good to test on both directed and undirected.  
  
I know that we're primarily testing for a segfault, but it looks weird in a test if there is no accompanying assertion, so please add in a BOOST_TEST for i) the expected number of vertices and ii) edges after calling `remove_vertex`.  
  
Thank you!!

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2022-01-05 22:53:06 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1006140531  

Looks great, thanks. As soon as the CI is fixed, I'll merge this.

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2022-02-04 04:27:12 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1029623165  

Please update to the latest changes on `develop` so we can test for a green build.

---

## Comment 8

> Username: cqc-alec  
> Created_at: 2022-02-04 09:58:35 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1029820125  

> Please update to the latest changes on `develop` so we can test for a green build.  
  
Done. Still seems to be one build failure (looks unrelated) ...?

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2022-02-05 19:51:00 UTC  
> Updated_at: 2022-02-05 19:51:24 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1030688496  

Yeah, that is strange and annoying.

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2022-02-07 23:24:42 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1032039932  

Might be a real issue with Boost.Filesystem, let's see: https://github.com/boostorg/filesystem/issues/227

---

## Comment 11

> Username: jeremy-murphy  
> Created_at: 2022-02-08 22:29:32 UTC  
> Updated_at: 2022-02-08 22:29:52 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1033127727  

The simplest way I know to re-run the CI is to close the PR and open it again!

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2022-02-08 22:31:11 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1033128819  

Hmmm, that didn't work as I expected.

---

## Comment 13

> Username: jeremy-murphy  
> Created_at: 2022-02-20 01:47:40 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1046140697  

A couple of issues have been fixed so I think if you merge develop now, you should get a green build.

---

## Comment 14

> Username: cqc-alec  
> Created_at: 2022-02-20 08:48:46 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1046191253  

> A couple of issues have been fixed so I think if you merge develop now, you should get a green build.  
  
Done: seems good now!

---

## Comment 15

> Username: jeremy-murphy  
> Created_at: 2022-02-21 00:44:35 UTC  
> Url: https://github.com/boostorg/graph/pull/269#issuecomment-1046365297  

Awesome!  
  
In the future, I think it's good to refer to the commit that you're reverting (I put it in the merge commit), so that people can easily track the history.

---
