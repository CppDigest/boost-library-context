# #1400 experimental: use clang-format for buffer and graph [Open]

> Username: barendgehrels  
> Created at: 2025-04-26 10:51:27 UTC  
> Updated at: 2025-05-16 09:44:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400  

# Applied on two folders only!  
  
This is experimental and verifies how `clang-format` could be used for our code base,  
using a style that is most close to what we (in general) use.  
  
  
I used `clang-format-15`.   
  
## Other Boost libraries  
`clang-format` is used by a few other boost libraries. I tried some of these configurations, but it was not satisfactory to my taste.  
Here an indication (on a branch of somewhere last year), with sizes in bytes:  
  
```  
  414  ./graph/.clang-format  
  706  ./multiprecision/.clang-format  
 1721  ./outcome/.clang-format  
 2561  ./histogram/.clang-format  
 3111  ./yap/.clang-format  
 4108  ./nowide/.clang-format  
 5697  ./locale/.clang-format  
  
  685 ./geometry/.clang-format  
```

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 10:52:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795920341  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_box.hpp

```diff
  18 |+ namespace boost
  19 |+ {
  20 |+ namespace geometry
```

> Username: barendgehrels  
> Created_at: 2025-04-26 10:52:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061259626  

I cannot find a rule which doesn't affect this style for namespace.  
Maybe we can live with this change.

> Username: tinko92  
> Created_at: 2025-04-27 06:40:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2062431715  

With the more detailled Brace rules as in https://github.com/boostorg/geometry/pull/1400#issuecomment-2833213438 , I can get `namespace boost { namespace geometry {`

> Username: barendgehrels  
> Created_at: 2025-04-27 08:51:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2062567210  

Yes, better for sure! Thanks! Applied!


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 10:52:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795921066  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 141 | 
 152 |-     using piece_border_type = piece_border<Ring, point_type> ;
 142 |+     using piece_border_type = piece_border<Ring, point_type>;
```

> Username: barendgehrels  
> Created_at: 2025-04-26 10:52:55 UTC  
> Updated_at: 2025-04-26 10:52:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061259742  

It nicely catches these kind of small errors.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 10:53:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795921751  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 190 |+               is_flat_start(false),
 191 |+               is_flat_end(false),
 192 |+               is_deflated(false)
```

> Username: barendgehrels  
> Created_at: 2025-04-26 10:53:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061259816  

This is also changed, I cannot find how to leave the comma before.  
Also here, maybe we can live with it

> Username: barendgehrels  
> Created_at: 2025-04-26 12:03:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061270628  

Fixed using `BreakConstructorInitializers`


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 10:56:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795924094  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
  42 |+           typename Pieces,
  43 |+           typename DistanceStrategy,
  44 |+           typename UmbrellaStrategy
```

> Username: barendgehrels  
> Created_at: 2025-04-26 10:56:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061260375  

This is also different than what we had. But it is (as far as I can see) the variant closest possible.


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 10:57:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795925384  

📁 include/boost/geometry/algorithms/detail/overlay/graph/get_tois.hpp

```diff
  82 |+             = get_turn_indices_by_node_id(turns, clusters, source_node_id, allow_closed);
  83 |+         auto const target_turn_indices
  84 |+             = get_turn_indices_by_node_id(turns, clusters, target_node_id, allow_closed);
```

> Username: barendgehrels  
> Created_at: 2025-04-26 10:57:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061260583  

Nice catches of wrong `const` placements, from me recently...


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 10:57:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795925874  

📁 include/boost/geometry/algorithms/detail/overlay/graph/get_tois.hpp

```diff
 100 |+         std::cout << "quadratic: " << source_node_id << " -> " << target_node_id << " sizes "
 101 |+                   << source_turn_indices.size() << " x " << target_turn_indices.size() << " = "
 102 |+                   << result.size() << std::endl;
```

> Username: barendgehrels  
> Created_at: 2025-04-26 10:57:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061260656  

This is not ideal IMO but I don't think we can get this completely satisfactory


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 10:59:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795926652  

📁 include/boost/geometry/algorithms/detail/overlay/graph/is_target_operation.hpp

```diff
 166 |+ struct is_better_collinear_target
 167 |+ {
 168 |+ };
```

> Username: barendgehrels  
> Created_at: 2025-04-26 10:59:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061260800  

This is somehow split (though I have `AllowShortBlocksOnASingleLine`)

> Username: tinko92  
> Created_at: 2025-04-27 06:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2062434823  

With the more detailled Brace rules as in https://github.com/boostorg/geometry/pull/1400#issuecomment-2833213438 , I can get   
  
```  
template <operation_type Operation>  
struct is_better_collinear_target  
{};  
```


---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 11:07:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795933674  

📁 include/boost/geometry/algorithms/detail/overlay/graph/select_edge.hpp

```diff
 298 | 
 302 |- private:
 299 |+     private:
```

> Username: barendgehrels  
> Created_at: 2025-04-26 11:07:14 UTC  
> Updated_at: 2025-04-26 11:07:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061262046  

This could be fixed (`-4`)


---

## Review 9 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 11:07:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795934029  

📁 include/boost/geometry/algorithms/detail/overlay/graph/select_edge.hpp

```diff
 294 |-                         op0, op1, edges.front().toi, edges.back().toi);
 290 |+                 bool const better
 291 |+                     = is_better_collinear_for_union(op0, op1, edges.front().toi, edges.back().toi);
```

> Username: barendgehrels  
> Created_at: 2025-04-26 11:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061262081  

This is nicer IMO


---

## Review 10 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 11:10:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795935394  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 203 |+                : side == 1                  ? geometry::strategy::buffer::join_concave
 204 |+                : same_direction(p0, p1, p2) ? geometry::strategy::buffer::join_continue
 205 |+                                             : geometry::strategy::buffer::join_spike;
```

> Username: barendgehrels  
> Created_at: 2025-04-26 11:10:01 UTC  
> Updated_at: 2025-04-26 11:10:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061262392  

This is not too bad, actually better than I've ever seen from clang-format


---

## Review 11 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 11:11:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1400#pullrequestreview-2795935644  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 683 |+                                                                       first_p2,
 684 |+                                                                       last_p1,
 685 |+                                                                       last_p2);
```

> Username: barendgehrels  
> Created_at: 2025-04-26 11:11:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#discussion_r2061262734  

not really nice... but still acceptable probably


---

## Comment 12

> Username: tinko92  
> Created_at: 2025-04-27 06:39:26 UTC  
> Updated_at: 2025-04-27 07:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#issuecomment-2833213438  

This looks very convenient and I think having a style that can be programmatically enforced but is slightly different from before is better than having a style that is more difficult to ensure consistency for, I'm definitely in favour of this change.  
  
The behaviour for namespaces and empty structs can maybe be made closer to the original by breaking up the Allman style into custom rules to partially address https://github.com/boostorg/geometry/pull/1400#discussion_r2061260800 and https://github.com/boostorg/geometry/pull/1400#discussion_r2061259626 like this:  
  
```diff  
-BreakBeforeBraces: Allman  
+BreakBeforeBraces: Custom  
+BraceWrapping:  
+  AfterClass: true  
+  AfterControlStatement: true  
+  AfterEnum: true  
+  AfterFunction: true  
+  AfterNamespace: false  
+  AfterStruct: true  
+  AfterUnion: true  
+  AfterExternBlock: true  
+  BeforeCatch: true  
+  BeforeElse: true  
+  BeforeLambdaBody: true  
+  SplitEmptyFunction: false  
+  SplitEmptyRecord: false  
+  SplitEmptyNamespace: false  
+  IndentBraces: false  
```  
  
The following may also be of interest, if this is to be mass-applied, to somewhat preserve the ability to use git blame to understand why some particular line was changed, it requires logging the commit in a file called .git-blame-ignore-revs at root.  
https://docs.github.com/en/repositories/working-with-files/using-files/viewing-and-understanding-files#ignore-commits-in-the-blame-view

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2025-05-15 17:02:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#issuecomment-2884505090  

hi @vissarion , I'll not merge it - but just to gauge your opinion, do you like it and the proposed styling?

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2025-05-15 17:04:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#issuecomment-2884510434  

> The following may also be of interest, if this is to be mass-applied, to somewhat preserve the ability to use git blame to understand why some particular line was changed, it requires logging the commit in a file called .git-blame-ignore-revs at root.  
> https://docs.github.com/en/repositories/working-with-files/using-files/viewing-and-understanding-files#ignore-commits-in-the-blame-view  
  
Very interesting, thanks! So we should use that indeed.

---

## Comment 15

> Username: vissarion  
> Created_at: 2025-05-16 09:44:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1400#issuecomment-2886218276  

> hi @vissarion , I'll not merge it - but just to gauge your opinion, do you like it and the proposed styling?  
  
I definitely like it and I am in favor of using `clang-format`.

---
