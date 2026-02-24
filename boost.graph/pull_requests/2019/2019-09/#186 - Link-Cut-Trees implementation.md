# #186 Link/Cut-Trees implementation [Open]

> Username: yi-ji  
> Created at: 2019-09-28 05:38:34 UTC  
> Updated at: 2025-12-31 07:18:17 UTC  
> Url: https://github.com/boostorg/graph/pull/186  

This PR implements Link/Cut-Trees data structure for Boost Graph Library. Since disjoint_sets algorithms has been moved into boost/graph (#169), this should also be the right place to put link_cut_trees algorithm.  
  
This implementation of link_cut_trees supports following operations:   
  
- `make_tree(x)`: create a new link/cut-tree containing only root `x`  
- `link(x, y)`: make the tree rooted at `x` a subtree of `y`;  
- `cut(x)`: remove the edge connecting `x` to its parent;  
- `find_root(x)`: find the root of the tree containing `x`;  
- `lowest_common_ancestor(x, y)`: find the lowest common ancestor of such `x` and `y` that have same root.  
  
All operations above are of amortized time complexity O(log n), except O(1) for `make_tree`.  
  
This implementation refers to:  
Robert Endre Tarjan, _Data Structures and Network Algorithms_, Chapter 5

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2019-09-29 10:20:25 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-536279656  

I'm not sure that Boost.Graph really is the right place for `disjoint_sets`, but it definitely is the right place  for `link_cut_tree`!  
I just have one question: how is the documentation going?  :)

---

## Review 2 [Commented]

> Username: jeremy-murphy  
> Created_at: 2019-09-29 10:26:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-294677833  

📁 include/boost/graph/link_cut_tree.hpp

```diff
 274 |+         std::unordered_map<Index, Vertex> id_to_vertex;
 275 |+         IndexMapContainer parent_map, left_map, right_map;
 276 |+     };
```

> Username: jeremy-murphy  
> Created_at: 2019-09-29 10:26:34 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r329348121  

Did you consider parameterizing the associative container for `id_to_vertex`? Someone might have a use case where they can just use `std::vector` and they'll be awfully annoyed that `std::unordered_map` is hard-coded.

> Username: yi-ji  
> Created_at: 2019-10-06 14:23:45 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r331792135  

Thanks for advice, it's fixed. Also added documentation :)


---

## Comment 3

> Username: yi-ji  
> Created_at: 2019-10-09 12:56:10 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-539988167  

@jeremy-murphy comments resolved, checks passed :)

---

## Review 4 [Commented]

> Username: jeremy-murphy  
> Created_at: 2019-10-15 21:53:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-302220080  

📁 include/boost/graph/link_cut_trees.hpp

```diff
 231 |         typedef typename property_traits<ID>::value_type Index;
 232 |-         typedef std::unordered_map<Index, Index> IndexMapContainer;
 232 |+         typedef boost::unordered_map<Index, Index> IndexMapContainer;
```

> Username: jeremy-murphy  
> Created_at: 2019-10-15 21:53:05 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r335194769  

This looks like another associative container type that should be parameterized?

> Username: yi-ji  
> Created_at: 2019-10-16 14:36:20 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r335515960  

Thanks for the review!   
I thought about it and I actually don't think it should be. This type of container stores the parent/child mappings, which is something users shouldn't care about when they use `link_cut_trees_with_storage`.  
Similar way appears in `disjoint_sets_with_storage` [where](https://github.com/boostorg/graph/blob/develop/include/boost/pending/disjoint_sets.hpp#L127) a `std::vector` is used. In our case map containers are used to support more `Index` types, other than just [0, N] integers.

> Username: yi-ji  
> Created_at: 2019-10-25 09:57:15 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r338976254  

Hi @jeremy-murphy Can we mark this as resolved or do you think it's necessary to parameterize it? :)

> Username: jeremy-murphy  
> Created_at: 2020-06-11 05:08:09 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r438545167  

The problem is, the unordered containers in Boost and the standard library have pretty terrible performance characteristics. (Actually, I don't know that for a fact about Boost, I just assume that it is similar to `std`.)  
  
So, if I were to use this algorithm, and I cared about performance, the _first_ thing I would do is replace `boost::unordered_map` with a different associative container.  
And we can assume that people programming in C++ care about performance.  
There is a pretty good talk about hash table performance here: https://www.youtube.com/watch?v=M2fKMP47slQ  
  
So whilst we can't necessarily _provide_ our audience with the best hash table, we can at least _allow_ them to use one.  
And even if we did provide it... a better one would come along later.

> Username: jeremy-murphy  
> Created_at: 2020-06-18 07:45:23 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r442031739  

An afterthought: yeah, `disjoint_sets_with_storage` is hard-coded to use `std::vector`, and for a long time no-one would have challenged that. But now we, as a community, know that small-size optimizations can yield real benefits, so maybe even that data structure should become a parameter with `std::vector` as the default.  
  
Interesting comparison to note, [`std::stack`](https://en.cppreference.com/w/cpp/container/stack) has exactly this kind of parameterization, but it defaults to `std::dequeue` for some strange reason.

> Username: yi-ji  
> Created_at: 2020-06-20 07:49:53 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r443110888  

Makes sense to me, I agree. And I learned :+1:   
Done changing it as suggested :)


---

## Comment 5

> Username: yi-ji  
> Created_at: 2020-06-11 03:24:51 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-642382848  

Hi @jeremy-murphy  Thanks for reviewing! This PR has been ready for quite a while. Any concerns before merging it? Would appreciate someone with write access to have a look :) @jzmaddock

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2020-06-11 04:39:49 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-642401710  

Hey, sorry, I don't have much time for open-source projects now that I have a baby! I'll try to review again quickly.

---

## Review 7 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-06-11 04:41:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-428587908  

📁 doc/bibliography.html

```diff
 458 |+ <em>Linking and Cutting Trees</em><br>
 459 |+ Data Structures and Network Algorithms, ISBN: 978-0-89871-187-5, pp. 59-70, 1983.
 460 |+ 
```

> Username: jeremy-murphy  
> Created_at: 2020-06-11 04:41:13 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r438538942  

Wow, I'm surprised that this is the first time that this book is referenced -- probably long overdue!


---

## Review 8 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-06-11 04:43:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-428588629  

📁 doc/link_cut_trees.html

```diff
  53 |+   <pre>
  54 |+   ...
  55 |+   link_cut_trees&lt;ElementParentMap, ElementChildMap&gt; lct(p, l, r);
```

> Username: jeremy-murphy  
> Created_at: 2020-06-11 04:43:45 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r438539506  

Where do those parameters--p, l, r--come from? I might be missing it, but I can't see them defined earlier.

> Username: yi-ji  
> Created_at: 2020-06-20 07:41:12 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r443110360  

This code snippet is a tiny example showing the basic usage, with variables declaration/initiation omitted. Runnable examples can be found in `test/link_cut_trees_test.cpp`.  
With that being said, I've updated their names to improve readability.


---

## Review 9 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-06-12 02:39:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-429425207  

📁 include/boost/graph/link_cut_trees.hpp

```diff
  23 |+         inline link_cut_trees(ElementParentMap p, ElementChildMap l, ElementChildMap r) : parent(p), left(l), right(r) {}
  24 |+         
  25 |+         inline link_cut_trees(const link_cut_trees<ElementParentMap, ElementChildMap> &c)
```

> Username: jeremy-murphy  
> Created_at: 2020-06-12 02:39:36 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r439176452  

This is an unimportant topic but you might like to know that you can just use the class name (e.g. `link_cut_trees`) inside the class without the template parameters and it will automatically use the current values of them. It can help make code a little more readable.

> Username: yi-ji  
> Created_at: 2020-06-20 07:40:34 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r443110315  

Good to know! Fixed it.


---

## Review 10 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-06-12 02:41:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-429425699  

📁 include/boost/graph/link_cut_trees.hpp

```diff
  40 |+         template <class Element>
  41 |+         inline void link(Element x, Element y) // Element x must be a tree root
  42 |+         {
```

> Username: jeremy-murphy  
> Created_at: 2020-06-12 02:41:17 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r439176855  

Can you assert that x is a tree root so that making that mistake is caught during debugging? Maybe it's too difficult to test, I haven't actually looked into it yet.

> Username: jeremy-murphy  
> Created_at: 2020-06-18 04:37:08 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r441963500  

But looking at it again, maybe:  
`BOOST_ASSERT(find_root(x) == x);`

> Username: yi-ji  
> Created_at: 2020-06-20 07:41:59 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r443110397  

The amortized time complexity is _O(log n)_ for both `link` and `find_root`; However, if `find_root` is called inside each `link` call, then I'm afraid the amortized time complexity might not remain the same.

> Username: jeremy-murphy  
> Created_at: 2020-06-22 04:57:14 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r443317013  

That's true, it will make the time complexity worse. In my opinion, that's OK in Debug mode, where a developer is concerned with correctness, not performance.  
I definitely think this is a nice safeguard to have, but it's not critical.  
  
Let's ask @jzmaddock what he thinks.

> Username: yi-ji  
> Created_at: 2020-06-23 14:55:41 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r444289501  

@jeremy-murphy I've just realized that `BOOST_ASSERT` can be optinally disabled using certain [macros](https://www.boost.org/doc/libs/1_67_0/libs/assert/doc/html/assert.html#boost_assert). So I've added the assertion as suggested :) If users would like to skip them for best performance, they can easily achieve that.

> Username: jeremy-murphy  
> Created_at: 2020-06-25 05:58:33 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r445323628  

Ah, OK, cool, that way everyone is happy.


---

## Review 11 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-06-18 04:37:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-432934931  

📁 include/boost/graph/link_cut_trees.hpp

```diff
  57 |+         template <class Element>
  58 |+         inline Element lowest_common_ancestor(Element x, Element y) // Elements x and y must have same root
  59 |+         {
```

> Username: jeremy-murphy  
> Created_at: 2020-06-18 04:37:56 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r441963692  

Again, something like `BOOST_ASSERT(find_root(x) == find_root(y));`

> Username: yi-ji  
> Created_at: 2020-06-23 14:56:03 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r444289812  

Done adding it.


---

## Comment 12

> Username: yi-ji  
> Created_at: 2020-06-21 04:10:46 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-647076371  

@jeremy-murphy  Thanks for helping review again :smile:   
I've replied or made changes accordingly.   
Enjoy parenting! :baby_bottle:

---

## Comment 13

> Username: yi-ji  
> Created_at: 2020-06-24 13:46:25 UTC  
> Updated_at: 2020-06-25 12:59:13 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-648830570  

Hi @jeremy-murphy good sir, all comments resolved & checks passed, ready again :)    
  
Hope this is not disturbing you @jzmaddock Would you mind having a look to help merge it? :))

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2020-10-10 06:44:18 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-706498885  

I can't merge it, sorry, I don't have that access. @jzmaddock is the only person I'm aware of with that access and is active at the moment.

---

## Comment 15

> Username: yi-ji  
> Created_at: 2021-03-09 08:39:41 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-793554248  

This has been lying in backlog for quite a while. @jzmaddock would you mind having a look? :)

---

## Review 16 [Commented]

> Username: jeremy-murphy  
> Created_at: 2021-03-22 23:27:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-618060393  

📁 include/boost/graph/link_cut_trees.hpp

```diff
  30 |+         {
  31 |+             make_path(x);
  32 |+         }
```

> Username: jeremy-murphy  
> Created_at: 2021-03-22 23:27:21 UTC  
> Updated_at: 2021-03-24 08:29:02 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r599141602  

Hey, I was just wondering... do you actually need to use the `inline` keyword on all these member functions?   
I'm not sure it has any effect, because I think they are all inline by virtue of being defined _in situ_.  
It will just simplify reading the code a bit if we can remove them.

> Username: yi-ji  
> Created_at: 2021-03-26 09:35:25 UTC  
> Updated_at: 2021-03-26 09:35:26 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r602137190  

Yeah I agree, I have them removed now, thanks.  
The CI builds are failing, but the error logs seem totally unrelated.   
Even a blank change failed to build: https://github.com/boostorg/graph/pull/243

> Username: jeremy-murphy  
> Created_at: 2021-03-29 22:36:49 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r603657825  

Dang, I can't help with the CI stuff, sorry.

> Username: jeremy-murphy  
> Created_at: 2021-04-25 23:48:19 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r619899831  

Yeah, could be that the develop branch needs some cleaning up before anyone can merge new features.

> Username: yi-ji  
> Created_at: 2021-06-13 13:37:43 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r650528114  

No problem, CI builds passed now


---

## Comment 17

> Username: xiuliren  
> Created_at: 2021-04-14 17:42:17 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-819698958  

thanks for adding this!  
It seems that this data structure could not be serialized using Boost: Serialization?

---

## Comment 18

> Username: yi-ji  
> Created_at: 2021-06-13 13:53:03 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-860214597  

@jingpengw I guess it depends on the template parameters? All I know is that boost serialization supports [POD](https://en.cppreference.com/w/cpp/named_req/PODType) and STL containers, I think you can use them to instantiate link-cut trees.

---

## Comment 19

> Username: yi-ji  
> Created_at: 2021-08-01 15:24:42 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-890539349  

@jzmaddock Could you please take a look at this MR when you've got time? It's ready for merge :) Thank you.

---

## Comment 20

> Username: xiuliren  
> Created_at: 2021-11-11 03:23:56 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-965955043  

@jzmaddock did you get a chance to take a look? is it good to merge?

---

## Comment 21

> Username: jeremy-murphy  
> Created_at: 2021-12-09 05:59:28 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-989540288  

I need to get the CI fixed and I'm going to prioritize merging some bug fixes, but then one day I'll get back to you!  ;)

---

## Comment 22

> Username: xiuliren  
> Created_at: 2023-10-25 14:02:34 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-1779349967  

any chance to merge this?

---

## Comment 23

> Username: jeremy-murphy  
> Created_at: 2023-10-26 23:51:41 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-1782084510  

> any chance to merge this?  
  
Yes, there is a chance.  :) I'll try to look over it again soon, but I can't guarantee anything.

---

## Comment 24

> Username: jeremy-murphy  
> Created_at: 2023-10-29 22:27:41 UTC  
> Url: https://github.com/boostorg/graph/pull/186#issuecomment-1784247555  

> > any chance to merge this?  
>   
> Yes, there is a chance. :) I'll try to look over it again soon, but I can't guarantee anything.  
  
Btw, what I meant is, I definitely want to merge it, it's just a matter of chance that I find time to do it.  :)

---

## Review 25 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-10-29 23:26:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-1703069115  

📁 include/boost/graph/link_cut_trees.hpp

```diff
  17 |+ namespace boost
  18 |+ {
  19 |+     template<class ElementParentMap, class ElementChildMap = ElementParentMap>
```

> Username: jeremy-murphy  
> Created_at: 2023-10-29 23:26:50 UTC  
> Updated_at: 2023-10-29 23:26:51 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r1375540079  

I presume that `Element` in the Parent and Child maps have to be the same type, right? Or at the very minimum, they must be convertible to each other? The reason I ask is that I presume we can simplify the member functions from being templated on `Element` to being non-template functions. If we impose the strict requirement of being the same type then we can just get the element type from one of the map types, something like:  
```  
using Element = ElementParentMap::key_type;  
```  
Or if we want to allow more flexibility, then it might have to be:  
```  
using Element = typename std::common_type_t<ElementParentMap::key_type, ElementChildMap::key_type>;  
```  
  
What do you think?


---

## Review 26 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-10-29 23:38:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-1703081536  

📁 include/boost/graph/link_cut_trees.hpp

```diff
 180 |+             return x;
 181 |+         }
 182 |+ 
```

> Username: jeremy-murphy  
> Created_at: 2023-10-29 23:38:53 UTC  
> Updated_at: 2023-10-29 23:38:54 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r1375548625  

Not sure if this function needs to exist?


---

## Review 27 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-10-29 23:41:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/186#pullrequestreview-1703082074  

📁 include/boost/graph/link_cut_trees.hpp

```diff
 221 |+     template <class ID = identity_property_map, 
 222 |+         class InverseID = boost::unordered_map<typename property_traits<ID>::value_type, typename property_traits<ID>::key_type>, 
 223 |+         class IndexMapContainer = boost::unordered_map<typename property_traits<ID>::value_type, typename property_traits<ID>::value_type> >
```

> Username: jeremy-murphy  
> Created_at: 2023-10-29 23:41:46 UTC  
> Updated_at: 2023-10-29 23:41:47 UTC  
> Url: https://github.com/boostorg/graph/pull/186#discussion_r1375549106  

Boost.Unordered has a new, much faster, hash map called `unordered_flat_map`: https://www.boost.org/doc/libs/develop/libs/unordered/doc/html/unordered.html  
  
It's not compliant with `unordered_map`, but I presume we don't need the extra features of the standard interface and can drop it in here.


---
