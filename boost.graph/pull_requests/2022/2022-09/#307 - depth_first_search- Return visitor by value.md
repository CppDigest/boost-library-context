# #307 depth_first_search: Return visitor by value [Open]

> Username: rkohrt  
> Created at: 2022-09-12 15:45:40 UTC  
> Updated at: 2023-09-13 01:09:05 UTC  
> Url: https://github.com/boostorg/graph/pull/307  

In the documentation the `DFSVisitor` is taken by reference: https://www.boost.org/doc/libs/1_80_0/libs/graph/doc/depth_first_visit.html , while in the actual header it is taken by copy.  
I assume the code in the header is by mistake and I would actually like it to take the visitor by reference for my usecase.  
  
As far as I can tell this mismatch existed already from the beginning: https://github.com/boostorg/graph/commit/121bb31837075fe98dfc44b00edc6c382ec394b8

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-09-15 00:45:41 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1247442518  

Yes, the algorithm has `DFVisitor&` in the declaration, but if you search for all the references to "visitor" on that page, you'll notice that it is otherwise defined as being taken by value.  
  
If you recall, visitors to STL algorithms (such as `std::for_each`) are also taken by value, so that is the 'correct' thing to do here. Unfortunately, this algorithm doesn't implement the complete visitor design, which should include returning the visitor by value at the end.  
  
So the real fix is to change these algorithms from returning `void` to returning `DFSVisitor` by value.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2022-09-15 20:49:08 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1248614437  

Thanks for the quick response. For a moment there I thought that `detail::depth_first_visit_impl` needed to be changed in the same way, but since it already takes the visitor by reference, well, let's leave it that way.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2022-09-15 20:52:06 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1248617187  

We can remove that [1] note now about a mutable visitor needing to hold data by pointer/reference.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2022-09-16 00:01:13 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1248767748  

Great, all tests pass, nothing broken!

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2022-09-16 00:18:55 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1248777883  

Oh, now I just realized that this is only the depth_first_VISIT page, so we also have to modify the documentation page for depth_first_SEARCH.  
  
There is also the BREADTH_first algorithm to consider. It would be odd to change one and not the other.  
  
Also... we do need a test to show that the new interface works. Nothing complicated, just something that shows that a mutating visitor returns in the expected state. One that counts the number of times each different visit function is called would be good. When it is returned, you assert the counts equal what you expect.  
  
I know this is more work than you expected, but it is a really positive improvement for the library. I'll advise you through it and merge it at the end.

---

## Comment 6

> Username: rkohrt  
> Created_at: 2022-09-16 12:15:50 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1249292090  

Okay, will make these changes next week

---

## Comment 7

> Username: rkohrt  
> Created_at: 2022-09-27 10:39:13 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1259314342  

Will need to have a closer look at the breath first search to make the change there, because the interface is different. But here is the test for the dfv

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2022-09-28 03:41:56 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1260345739  

It's looking really good!

---

## Review 9 [Commented]

> Username: jeremy-murphy  
> Created_at: 2022-09-28 03:49:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/307#pullrequestreview-1122973460  

📁 test/dfv_test.cpp

```diff
  86 |+   counting_dfs_visitor visitor_copy = depth_first_visit(g, vertex(u, g), counting_dfs_visitor(), color);
  87 |+   BOOST_TEST(visitor_copy.vertex_events == 6u*2u + 1u); // discover_vertex + finish_vertex for each vertex and once start_vertex
  88 |+   BOOST_TEST(visitor_copy.seen_edges == 2u*9u);
```

> Username: jeremy-murphy  
> Created_at: 2022-09-28 03:46:51 UTC  
> Updated_at: 2022-09-28 03:49:34 UTC  
> Url: https://github.com/boostorg/graph/pull/307#discussion_r981914190  

Magic numbers are easily confusing; presumably `6 = g.num_vertices()`, and `9 = g.num_edges()`?


---

## Review 10 [Commented]

> Username: jeremy-murphy  
> Created_at: 2022-09-28 03:52:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/307#pullrequestreview-1122976305  

📁 test/dfv_test.cpp

```diff
  61 |+ 
  62 |+   size_t vertex_events = 0;
  63 |+   size_t seen_edges = 0;
```

> Username: jeremy-murphy  
> Created_at: 2022-09-28 03:52:41 UTC  
> Url: https://github.com/boostorg/graph/pull/307#discussion_r981916168  

Ah, this is a compilation error in C++03, sorry. You'll have to initialize them the old-fashioned way, in the constructor.


---

## Comment 11

> Username: badfilms  
> Created_at: 2022-10-05 02:53:32 UTC  
> Updated_at: 2022-10-05 03:24:53 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1267845433  

I believe that the issue with passing a visitor by value has to do with when the DFS implementation is recursive, which is why the source for that implementation explicitly has the following comment:  
  
https://github.com/boostorg/graph/blob/2862644154f35bf1db130b984a22fa4ea3dff955/include/boost/graph/depth_first_search.hpp#L224  
  
From my experience, this is due to the fact that visitors are inherited from (or otherwise defined in a manner that meets the visitor concept), and thus we cannot expect a user-defined visitor to not have state (which will not be maintained in the current recursive implementation unless passed by reference).  
  
Obviously, the recursive implementation shouldn't be preferred to the iterative one in the majority of use cases, and I believe is more of a legacy implementation from many years ago, but it is worth noting.  
  
But the inconsistencies make the design decision less transparent. Because of the point mentioned above regarding state, I was always under the impression that it was due to the fact that passing a visitor by value has the potential to be expensive--unlike the higher order functions passed by value to algorithms like `std::for_each`, which just take an invokable that should be trivially copyable.

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2022-10-06 03:58:32 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1269277219  

Hey @badfilms , thanks for joining in and pointing out those facts that yes, make the original design decision less transparent and the best way forward now less obvious.   
  
I'm still happy to proceed with the design that I have suggested, copying the visitor, until a definite red flag appears.  
  
> But the inconsistencies make the design decision less transparent. Because of the point mentioned above regarding state, I was always under the impression that it was due to the fact that passing a visitor by value has the potential to be expensive--unlike the higher order functions passed by value to algorithms like `std::for_each`, which just take an invokable that should be trivially copyable.  
  
It's worth noting that `std::for_each` actually returns the visitor passed into it, which is only done because the visitor might have state that you want to inspect after the algorithm completes. In fact, one might even argue that the _only_ reason to use `std::for_each` is to apply a stateful 'visitor' and inspect it at the end. (Any transformation (i.e. mutation) of the elements can be accomplished using `std::transform`.)  
  
Having state does not mean copying is expensive, right? It's only if the copy/assignment constructor is non-trivial that it becomes an issue. Now if a visitor wanted to have a huge amount of state, then I would say it should just hold a pointer to it -- which itself is trivially copyable. And this argument goes for both 'visitors' in `std::for_each` and graph algorithms.  
  
But on the other hand, I can't really argue _against_ taking visitors by reference either. `¯\_(ツ)_/¯`

---

## Comment 13

> Username: badfilms  
> Created_at: 2022-10-06 04:20:00 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1269289689  

> It's worth noting that std::for_each actually returns the visitor passed into it, which is only done because the visitor might have state that you want to inspect after the algorithm completes. In fact, one might even argue that the only reason to use std::for_each is to apply a stateful 'visitor' and inspect it at the end. (Any transformation (i.e. mutation) of the elements can be accomplished using std::transform.)  
  
@jeremy-murphy  Ahh right. I clearly wasn't thinking when I wrote that because my brain was differentiating a `DFSVisitor`, for example, from a visitor to a `std` algorithm due to the fact that it has multiple points of interest, forgetting the case of a function object.

---

## Review 14 [Commented]

> Username: jeremy-murphy  
> Created_at: 2022-10-14 05:40:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/307#pullrequestreview-1141840419  

📁 test/dfv_test.cpp

```diff
  66 |+ 
  67 |+   size_t vertex_events;
  68 |+   size_t seen_edges;
```

> Username: jeremy-murphy  
> Created_at: 2022-10-14 05:40:20 UTC  
> Updated_at: 2022-10-14 05:40:21 UTC  
> Url: https://github.com/boostorg/graph/pull/307#discussion_r995359658  

I would actually be tempted to count each distinct event separately, to get a complete picture of what happens.  
This would also help catch any subtle bugs.


---

## Comment 15

> Username: jeremy-murphy  
> Created_at: 2023-09-13 01:09:04 UTC  
> Url: https://github.com/boostorg/graph/pull/307#issuecomment-1716786714  

I'm still keen to see this finished, btw. Are you still interested in working on it, @rkohrt ?

---
