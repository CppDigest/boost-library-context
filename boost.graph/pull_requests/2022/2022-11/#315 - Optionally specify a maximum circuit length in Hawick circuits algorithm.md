# #315 Optionally specify a maximum circuit length in Hawick circuits algorithm [Merged]

> Username: svengato  
> Created at: 2022-11-26 17:21:52 UTC  
> Updated at: 2023-05-08 02:36:13 UTC  
> Merged at: 2023-05-08 01:57:33 UTC  
> Closed at: 2023-05-08 01:57:33 UTC  
> Url: https://github.com/boostorg/graph/pull/315  

The number of circuits grows rapidly for larger graphs, so this is a way to reduce the computation time and still obtain useful partial results.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-03-10 00:02:12 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1463013005  

Looks good but needs at least one unit test to demonstrate that it works, i.e. showing that a non-zero `maxLength` will return a suboptimal answer.

---

## Review 2 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-03-10 00:03:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/315#pullrequestreview-1333989601  

📁 include/boost/graph/hawick_circuits.hpp

```diff
 155 |             VertexIndexMap const& vim, Stack& stack, ClosedMatrix& closed,
 156 |-             VerticesSize n_vertices)
 156 |+             VerticesSize n_vertices, unsigned int const maxLength)
```

> Username: jeremy-murphy  
> Created_at: 2023-03-10 00:03:20 UTC  
> Url: https://github.com/boostorg/graph/pull/315#discussion_r1131778818  

Don't `const` value parameters.  
```diff  
-            VerticesSize n_vertices, unsigned int const maxLength)  
+            VerticesSize n_vertices, unsigned int maxLength)  
```


---

## Review 3 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-03-10 00:35:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/315#pullrequestreview-1334010371  

📁 include/boost/graph/hawick_circuits.hpp

```diff
 355 |- void hawick_circuits(BOOST_FWD_REF(Graph) graph, BOOST_FWD_REF(Visitor) visitor)
 372 |+ void hawick_circuits(BOOST_FWD_REF(Graph) graph, BOOST_FWD_REF(Visitor) visitor,
 373 |+     unsigned int const maxLength = 0)
```

> Username: jeremy-murphy  
> Created_at: 2023-03-10 00:35:17 UTC  
> Updated_at: 2023-03-10 00:35:18 UTC  
> Url: https://github.com/boostorg/graph/pull/315#discussion_r1131802473  

```diff  
-    unsigned int const maxLength = 0)  
+    unsigned int maxLength = 0)  
```


---

## Review 4 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-03-10 00:35:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/315#pullrequestreview-1334010635  

📁 include/boost/graph/hawick_circuits.hpp

```diff
 369 |-     BOOST_FWD_REF(VertexIndexMap) vertex_index_map)
 388 |+     BOOST_FWD_REF(VertexIndexMap) vertex_index_map,
 389 |+     unsigned int const maxLength = 0)
```

> Username: jeremy-murphy  
> Created_at: 2023-03-10 00:35:41 UTC  
> Url: https://github.com/boostorg/graph/pull/315#discussion_r1131802790  

```diff  
-    unsigned int const maxLength = 0)  
+    unsigned int maxLength = 0)  
```

> Username: svengato  
> Created_at: 2023-03-10 01:44:53 UTC  
> Updated_at: 2023-03-10 01:44:54 UTC  
> Url: https://github.com/boostorg/graph/pull/315#discussion_r1131856012  

Thanks, I am removing the `const` from the `max_length` function arguments, plus some minor cleanup (like `max_length` instead of `maxLength`).  
  
One question: what about the data member `max_length_`? Should that remain `const`?


---

## Review 5 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-03-10 00:35:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/315#pullrequestreview-1334010780  

📁 include/boost/graph/hawick_circuits.hpp

```diff
 379 |-     BOOST_FWD_REF(Graph) graph, BOOST_FWD_REF(Visitor) visitor)
 399 |+     BOOST_FWD_REF(Graph) graph, BOOST_FWD_REF(Visitor) visitor,
 400 |+     unsigned int const maxLength = 0)
```

> Username: jeremy-murphy  
> Created_at: 2023-03-10 00:35:57 UTC  
> Updated_at: 2023-03-10 00:35:58 UTC  
> Url: https://github.com/boostorg/graph/pull/315#discussion_r1131802989  

```diff  
-    unsigned int const maxLength = 0)  
+    unsigned int maxLength = 0)  
```


---

## Comment 6

> Username: svengato  
> Created_at: 2023-03-10 03:58:04 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1463217462  

Unit tests work. Just waiting on the `const` data member question -- one thing I read suggests data members should not be `const` because then the data structure is not copy-assignable.

---

## Comment 7

> Username: svengato  
> Created_at: 2023-03-10 04:34:38 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1463239947  

> Just waiting on the `const` data member question --  
  
I decided to remove that last `const` and push the commits, that will give you more to work with. Let me know if any of the `const` need to be restored.

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2023-03-11 04:03:03 UTC  
> Updated_at: 2023-03-13 01:17:59 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1464817979  

> Unit tests work. Just waiting on the `const` data member question -- one thing I read suggests data members should not be `const` because then the data structure is not copy-assignable.  
  
Correct, but I think that class also has member references that will also prevent copying, so I didn't comment on it.

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2023-03-13 01:24:25 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1465369730  

> Unit tests work. Just waiting on the `const` data member question -- one thing I read suggests data members should not be `const` because then the data structure is not copy-assignable.  
  
Unfortunately, it looks like that `cycle_test` function is not really _testing_ the functionality, just reporting something to the console?  
What we need is a test that will produce a different result for `hawick_(unique_)circuits` depending on whether it was called with a custom `max_length` or not and fail if the wrong result is returned. Maybe I'm missing something, but I don't see that here.

---

## Comment 10

> Username: svengato  
> Created_at: 2023-03-13 01:28:22 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1465372005  

But it is reporting the correct result, the number of circuits with length up to 4.

---

## Comment 11

> Username: svengato  
> Created_at: 2023-03-13 02:19:56 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1465410005  

The current tests in `test/cycle_test.hpp` and `test/hawick_circuits.cpp` do not return a pass/fail value either. Are you looking for something like this?  
```  
// undirected, not unique, no maximum length  
BOOST_TEST(vis.cycles == 30);  
// directed, not unique, no maximum length  
BOOST_TEST(vis.cycles == 31);   
  
// undirected, unique, no maximum length  
BOOST_TEST(vis.cycles == 27);  
// directed, unique, no maximum length  
BOOST_TEST(vis.cycles == 24);  
  
// undirected, not unique, maximum length 4  
BOOST_TEST(vis.cycles == 28);  
// directed, not unique, maximum length 4  
BOOST_TEST(vis.cycles == 13);  
  
// undirected, unique, maximum length 4  
BOOST_TEST(vis.cycles == 25);  
// directed, unique, maximum length 4  
BOOST_TEST(vis.cycles == 10);  
```

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2023-03-13 04:00:17 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1465473842  

> The current tests in `test/cycle_test.hpp` and `test/hawick_circuits.cpp` do not return a pass/fail value either. Are you looking for something like this?  
>   
> ```  
> // undirected, not unique, no maximum length  
> BOOST_TEST(vis.cycles == 30);  
> // directed, not unique, no maximum length  
> BOOST_TEST(vis.cycles == 31);   
>   
> // undirected, unique, no maximum length  
> BOOST_TEST(vis.cycles == 27);  
> // directed, unique, no maximum length  
> BOOST_TEST(vis.cycles == 24);  
>   
> // undirected, not unique, maximum length 4  
> BOOST_TEST(vis.cycles == 28);  
> // directed, not unique, maximum length 4  
> BOOST_TEST(vis.cycles == 13);  
>   
> // undirected, unique, maximum length 4  
> BOOST_TEST(vis.cycles == 25);  
> // directed, unique, maximum length 4  
> BOOST_TEST(vis.cycles == 10);  
> ```  
  
Yes. And we should be able to demonstrate that the result changes appropriately as the value for `max_length` changes.

---

## Comment 13

> Username: svengato  
> Created_at: 2023-03-28 15:25:55 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1487108005  

The current tests are for no `max_length` and `max_length` = 4. Would you prefer that it test all `max_length` from 0 to 10 (length of the test graph's longest directed circuit)? That would be easy to implement.

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2023-05-04 06:09:44 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1534141376  

> The current tests are for no `max_length` and `max_length` = 4. Would you prefer that it test all `max_length` from 0 to 10 (length of the test graph's longest directed circuit)? That would be easy to implement.  
  
Hey sorry for the really delayed reply. Yes, I think testing a variety of values is better, and 0 to 10 sounds good without being excessive.

---

## Comment 15

> Username: svengato  
> Created_at: 2023-05-04 12:56:29 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1534730662  

Note that the longest circuit length is 10 for the directed test graph and 5 for the undirected test graph. As expected, `max_length` = 0 gives the same results as for no `max_length`, for `max_length` = 1 there are no circuits (the test graph has no loops), and for `max_length` &ge; 2 the result increases up to the total number of circuits.

---

## Comment 16

> Username: jeremy-murphy  
> Created_at: 2023-05-08 01:57:41 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1537630315  

Awesome, thanks for the great idea and contribution!

---

## Comment 17

> Username: svengato  
> Created_at: 2023-05-08 02:36:13 UTC  
> Url: https://github.com/boostorg/graph/pull/315#issuecomment-1537655791  

You are welcome!

---
