# #1011 Add support for GC in intersection, sym_difference and union. [Merged]

> Username: awulkiew  
> Created at: 2022-06-02 14:01:44 UTC  
> Updated at: 2022-06-29 20:11:47 UTC  
> Merged at: 2022-06-29 20:11:46 UTC  
> Closed at: 2022-06-29 20:11:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011  

These algorithms calculate the intermediate results based on the bounding boxes of elements of GCs and these results are merged together based on topological dimension. So e.g. parts of linestrings of GC1 that are intersecting any linear or areal geometry from GC2 are merged into multilinestring(s).  
  
The next step could be to subtract the results from each other to prevent them occupying the same space. It's not clear to me what the result of set operations should be for GCs because they are not well defined by the standard. I decided to leave these geometries as they are, so potentially intersecting each other, in order not to loose information.  
  
If merging of elements of the result is desired one may call `merge_elements()` afterwards.  
  
For example for the inputs, GC1:  
  
![gc1](https://user-images.githubusercontent.com/1226951/171644972-c8d85eef-820b-4455-bbc6-451f0aef777e.png)  
  
and GC2:  
  
![gc2](https://user-images.githubusercontent.com/1226951/171645144-41dcacab-b947-4000-ba54-47bcc17bafe2.png)  
  
the result of `intersection()` is:  
  
![intersection](https://user-images.githubusercontent.com/1226951/171833204-bfbe602e-817e-4a0e-83cf-85875846bcc3.png)  
  
(note that some of the linestrings are results of touching polygons, so intersecting boundaries)  
  
the result of `union()` is:  
  
![union](https://user-images.githubusercontent.com/1226951/171832639-9d58c69a-9975-4c5d-865f-c4d8d1c4a280.png)  
  
the result of `sym_difference()` is:  
  
![sym_difference](https://user-images.githubusercontent.com/1226951/171832825-312573dd-d2a6-4494-9353-5b7cb148ecec.png)

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-06-23 13:27:43 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1011#pullrequestreview-1016825754  

Thanks! LGTM.

📁 include/boost/geometry/algorithms/detail/intersection/gc.hpp

```diff
 213 |+     static void call_union(Out const& g1, Out const& g2, Out& out, Strategy const& strategy)
 214 |+     {
 215 |+         typedef typename geometry::rescale_overlay_policy_type
```

> Username: vissarion  
> Created_at: 2022-06-23 11:16:02 UTC  
> Updated_at: 2022-06-23 13:27:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r904896992  

you can use `using` as in the rest of the file

> Username: vissarion  
> Created_at: 2022-06-23 13:11:34 UTC  
> Updated_at: 2022-06-23 13:27:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r905004454  

Also using rescaling shouldn't be protected by `#ifdef`s as in other places in the library?

> Username: awulkiew  
> Created_at: 2022-06-24 23:11:52 UTC  
> Updated_at: 2022-06-24 23:11:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r906512173  

Done.  
  
Regarding #ifdefs, AFAIU flags are already checked inside `rescale_overlay_policy_type`.


📁 include/boost/geometry/algorithms/union.hpp

```diff
 508 |             <
 399 |-                 Collection
 509 |+             GeometryOut
```

> Username: vissarion  
> Created_at: 2022-06-23 13:25:08 UTC  
> Updated_at: 2022-06-23 13:27:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r905019256  

should keep the old indentation

> Username: awulkiew  
> Created_at: 2022-06-24 23:12:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r906512212  

Done


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2022-06-29 10:06:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1011#pullrequestreview-1023057212  

Thanks!  
Looks good to me in general.  
The PR's are large and therefore I'm delayed, sorry for that.

📁 include/boost/geometry/algorithms/detail/intersection/gc.hpp

```diff
  55 |+     std::enable_if_t<gc_can_move_element<GC, Multi>::value, int> = 0
  56 |+ >
  57 |+ inline void gc_move_multi_back_(GC& gc, Multi&& multi)
```

> Username: barendgehrels  
> Created_at: 2022-06-29 09:47:45 UTC  
> Updated_at: 2022-06-29 10:06:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r909429656  

Why do we suffix this with `_` ?  
Normally we put it in `namespace detail`

> Username: barendgehrels  
> Created_at: 2022-06-29 09:51:22 UTC  
> Updated_at: 2022-06-29 10:06:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r909433063  

Ah, it's already in `detail`

> Username: awulkiew  
> Created_at: 2022-06-29 19:57:11 UTC  
> Updated_at: 2022-06-29 20:00:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r910354638  

I renamed it to ~`gc_move_multi_or_element_back`~ `gc_move_one_elem_multi_back`.

---

📁 include/boost/geometry/algorithms/detail/intersection/gc.hpp

```diff
 187 |+                 bool r0 = unionize_result<0>(inters_result, out, strategy);
 188 |+                 bool r1 = unionize_result<1>(inters_result, out, strategy);
 189 |+                 bool r2 = unionize_result<2>(inters_result, out, strategy);
```

> Username: barendgehrels  
> Created_at: 2022-06-29 09:59:02 UTC  
> Updated_at: 2022-06-29 10:06:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r909440724  

Can we rename `unionize_result` and `call_union` somehow?  
Because `union` and `intersection` are two set operations we use extensively in our library, also with those names. To call something `union` from inside `intersection` looks confusing to me.  
`merge_result` maybe?  But you also already use `merge` somewhere.  
Synonyms suggest `combine`, `coalesce`, `meld` or just `join`

> Username: awulkiew  
> Created_at: 2022-06-29 19:56:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r910354388  

I renamed them to `merge_result` and `merge_two`.

---

📁 include/boost/geometry/algorithms/detail/intersection/gc.hpp

```diff
 185 |+                 // TODO: It'd probably be better to gather all of the parts first
 186 |+                 //       and then merge them with merge_elements.
 187 |+                 bool r0 = unionize_result<0>(inters_result, out, strategy);
```

> Username: barendgehrels  
> Created_at: 2022-06-29 09:59:41 UTC  
> Updated_at: 2022-06-29 10:06:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r909441323  

You can make them `const`

> Username: awulkiew  
> Created_at: 2022-06-29 19:57:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r910355201  

Done

---

📁 include/boost/geometry/algorithms/detail/intersection/gc.hpp

```diff
  75 |+ <
  76 |+     typename GC, typename Multi,
  77 |+     std::enable_if_t<! gc_can_move_element<GC, Multi>::value && ! gc_can_convert_element<GC, Multi>::value, int> = 0
```

> Username: barendgehrels  
> Created_at: 2022-06-29 10:05:54 UTC  
> Updated_at: 2022-06-29 10:06:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r909447094  

So we use `enable_if` here i/o tag dispatching because it's not really based on geometry tags, but more on just some conditions. That make sense to me for these cases.


📁 test/algorithms/set_operations/intersection/intersection_gc.cpp

```diff
 136 |+         "MULTILINESTRING((3 5,5 5,5 3),(4 3,4 4),(4 4,3 4),(3 3,4 4),(5 5,6 6),(5 1,5 3),(5 5,5 9),(5 2,9 2)),"
 137 |+         "MULTIPOLYGON(((3 4,4 4,4 3,3 3,3 4)),((6 6,10 6,10 4,6 4,6 0,4 0,4 1,5 1,5 5,4 5,1 5,1 4,0 4,0 6,4 6,4 10,6 10,6 6)))"
 138 |+     ")";
```

> Username: barendgehrels  
> Created_at: 2022-06-29 10:03:22 UTC  
> Updated_at: 2022-06-29 10:06:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r909444797  

Looks all good, thanks


📁 test/algorithms/set_operations/sym_difference/Jamfile

```diff
  19 |     [ run sym_difference_areal_areal.cpp   : : : : algorithms_sym_difference_areal_areal ]
  20 |     [ run sym_difference_linear_linear.cpp : : : : algorithms_sym_difference_linear_linear ]
  21 |+     [ run sym_difference_gc.cpp            : : : : algorithms_sym_difference_gc ]
```

> Username: barendgehrels  
> Created_at: 2022-06-29 10:04:20 UTC  
> Updated_at: 2022-06-29 10:06:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r909445704  

There is no unit test for `difference` yet?  
That would also be useful (now, or in a new PR)

> Username: vissarion  
> Created_at: 2022-06-29 10:21:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r909460225  

There should be one in `set_operations/difference` directory.   
  
On my side the line bellow `sym_difference_tupled` looks with wrong indentation.   
Also, how do we sort the contents in those Jamfiles they seem arbitrary to me.

> Username: awulkiew  
> Created_at: 2022-06-29 19:56:20 UTC  
> Updated_at: 2022-06-29 19:56:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1011#discussion_r910353953  

Yes, `difference` tests are in different directory.


---
