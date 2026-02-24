# #349 Isomorphism: Remove invariant contiguous range requirement [Merged]

> Username: jan-grimo  
> Created at: 2023-09-07 17:37:47 UTC  
> Updated at: 2024-05-30 23:45:21 UTC  
> Merged at: 2024-04-15 01:19:20 UTC  
> Closed at: 2024-04-15 01:19:20 UTC  
> Url: https://github.com/boostorg/graph/pull/349  

Completes isomorphism invariant improvements outlined in #203   
  
Invariant contiguous range requirement removal  
- Vertex invariants for use in isomorphism algorithm must no longer have low upper bounds due to a hidden allocation linear in the maximum encountered vertex invariant.  
- Vertex invariants must no longer be convertible to `size_t`, but can be any comparable and hashable types  
- Build `unordered_map`-backed invariant multiplicity map efficiently from sorted vertex invariants

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-09-08 01:16:02 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-1710946865  

Thanks for finishing this off!  
  
I might not be able to merge this until I fix the modernization issues that are causing our CI builds to fail.

---

## Comment 2

> Username: jan-grimo  
> Created_at: 2023-09-08 07:28:41 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-1711207305  

Oh, I thought that was me! No worries, I'm not in a hurry.

---

## Review 3 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-09-11 04:38:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1619111662  

📁 include/boost/graph/isomorphism.hpp

```diff
  41 |+         }
  42 |+ 
  43 |+         T t;
```

> Username: jeremy-murphy  
> Created_at: 2023-09-11 04:38:56 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1320985865  

Maybe I'm being too pedantic here, but declaring `T t;` here requires that `T` is `DefaultConstructible`, but is that necessary for the `Hashable` concept?

> Username: jan-grimo  
> Created_at: 2023-09-13 07:12:11 UTC  
> Updated_at: 2023-09-13 07:18:46 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1324073925  

No, that's a good catch, it should be unnecessary. I have to admit I found this exact concept in some other area of boost and didn't look at it closely enough.

> Username: jan-grimo  
> Created_at: 2023-09-13 08:06:31 UTC  
> Updated_at: 2023-09-13 08:07:04 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1324137300  

I can't use `declval` in this context though to avoid implying a particular construction method. I can avoid it by refactoring the whole invariant concept checking with C++11 features and `boost/type_traits` like so:  
  
```c++  
template < typename T >  
using TestHashable = decltype(hash<T>()(std::declval<T>()));  
  
template < typename T >  
struct Hashable: std::integral_constant<bool, boost::is_detected_v<TestHashable, T>> {};  
  
template < typename T >  
struct Invariant: std::integral_constant<bool, Hashable<T>::value && boost::has_less<T, T, bool>::value && boost::has_equal_to<T, T, bool>::value> {};  
```  
  
Downside is compiler error messages are probably worse than the boost concept checking variant. Assuming I didn't miss a way to avoid the default construction assumption, would you prefer I keep the `Hashable` concept as-is with default-construction or refactor it with `type_traits`?

> Username: jeremy-murphy  
> Created_at: 2023-09-20 05:04:41 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1330996375  

Ultimately, I think we shouldn't be defining a `Hashable` concept in an `isomorphism` algorithm file, we should be relying on an existing concept defined either in the standard library or a Boost concept library.  
I think that can be done in the future as part of a broader modernization change to Boost.Graph, so for now, I'm thinking just take the Hashable concept stuff out, don't worry about it.

> Username: jeremy-murphy  
> Created_at: 2023-09-20 05:06:16 UTC  
> Updated_at: 2023-09-20 05:06:17 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1330997134  

And the CI tests are failing because `concept` is now a reserved word.  :)


---

## Review 4 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:11:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1619188665  

📁 include/boost/graph/isomorphism.hpp

```diff
 106 |+                 return multiplicity.at(invariant1(x))
 107 |+                     < multiplicity.at(invariant1(y));
 108 |             }
```

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:11:06 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1321035819  

It's a bug if this function is called with invalid `x` or `y`, right? In which case, we should `assert` that they meet the relevant preconditions and ... I was going to say use member `operator[]`, but that's a bit misleading... so I guess it's member `find` and just assume that the iterator returned is not the end.  
But basically, let's not use `at` if we're sure that it should be there.


---

## Review 5 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:11:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1619189541  

📁 include/boost/graph/isomorphism.hpp

```diff
 109 |             Invariant1 invariant1;
  94 |-             size_type* multiplicity;
 110 |+             const multiplicity_map& multiplicity;
```

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:11:56 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1321036364  

References break regular copy semantics, so just use a pointer.


---

## Review 6 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:16:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1619194396  

📁 include/boost/graph/isomorphism.hpp

```diff
 176 | 
 177 |+         // Generates map of invariant multiplicity from sorted invariants
 178 |+         multiplicity_map multiplicities(const std::vector< invariant_t >& invariants) {
```

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:16:29 UTC  
> Updated_at: 2023-09-11 06:16:30 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1321039333  

Implement this as  
```diff  
-        multiplicity_map multiplicities(const std::vector< invariant_t >& invariants) {  
+        template <typename ForwardIterator>  
+        multiplicity_map multiplicities(ForwardIterator first, ForwardIterator last) {  
```


---

## Review 7 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:17:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1619195314  

📁 include/boost/graph/isomorphism.hpp

```diff
 177 |+         // Generates map of invariant multiplicity from sorted invariants
 178 |+         multiplicity_map multiplicities(const std::vector< invariant_t >& invariants) {
 179 |+           // Assumes invariants are sorted
```

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:17:16 UTC  
> Updated_at: 2023-09-11 06:17:17 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1321039921  

Don't just assume it, `assert` it!


---

## Review 8 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:37:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1619224548  

📁 include/boost/graph/isomorphism.hpp

```diff
 205 |+           }
 206 |+ 
 207 |+           return invar_multiplicity;
```

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:37:58 UTC  
> Updated_at: 2023-09-11 06:37:59 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1321057184  

So wait, this looks like it can be simplified to just call `invar_multiplicity[invar]++`, because if `invar` doesn't already exist in the map then `operator[]` creates it. Which means this function boils down to `std::for_each` with a pretty simple lambda like `[](auto invariant){ invariant_map[invariant]++; }`.   
Now, since Boost officially deprecated C++03, I think you can feel free to use C++14 if that simplifies the implementation, but don't do any mass conversion from C++03 to C++14.

> Username: jan-grimo  
> Created_at: 2023-09-13 07:07:11 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1324068860  

It's not exactly the same! So the sorted list of invariants is laid out like e.g. `2, 2, 2, 7, 7, 9, 13` etc., right? The proposed version above doesn't repeatedly find the key position in the map for equal successive invariants, while a `for_each` version does. I assume it makes more of a performance difference the more repeated values there are. The default vertex invariant is the vertex degree, of which there are probably many equal values in large graphs. I haven't benchmarked anything, but I'm perfectly willing to.

> Username: jeremy-murphy  
> Created_at: 2023-09-19 04:37:57 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1329566862  

Ah, no, you're right, it would be silly to do it the way I suggested.


---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2023-09-11 06:54:02 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-1713275930  

Btw, we generally put the `{` on a new line after `if`, etc, so please keep to that format.

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2023-09-19 04:39:04 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-1724820530  

Can you merge `develop` into your branch to get the updated CI configuration please?

---

## Review 11 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-09-20 23:36:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1636654448  

📁 include/boost/graph/isomorphism.hpp

```diff
 187 |+                 return invar_multiplicity;
 188 |+             
 189 |+             invar_iter it = std::move(first);
```

> Username: jeremy-murphy  
> Created_at: 2023-09-20 23:36:04 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1332284220  

Ahh, not sure what you're thinking here? Just keep using `first`, no need to copy/move it.

> Username: jan-grimo  
> Created_at: 2023-09-21 08:41:21 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1332697695  

I always thought range algorithms modifying `first` weren't naming their variables right :man_shrugging: I've removed it.

> Username: jeremy-murphy  
> Created_at: 2023-09-22 00:38:12 UTC  
> Updated_at: 2023-09-22 00:38:13 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1333740357  

I get what you mean, but I guess one way to think about it is that it's named for what the user passes in. What you do with it inside the algorithm is up to you. And consider, what is the meaning of `it`?

> Username: jan-grimo  
> Created_at: 2023-09-22 06:49:22 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1333952245  

That's fair enough, `it` isn't the best variable name either. Could be short for iterator or item, which doesn't help all that much. `iter` might have been better. In any case, I think it's fine the way it is now.


---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2024-04-03 00:17:05 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2033314915  

I've forgotten where I was up to on this.  :\

---

## Review 13 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-04-03 00:44:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1975299485  

📁 include/boost/graph/isomorphism.hpp

```diff
 212 |+             sort(invar2_array);
 213 |+             if (!equal(invar1_array, invar2_array))
 214 |+                 return false;
```

> Username: jeremy-murphy  
> Created_at: 2024-04-03 00:44:01 UTC  
> Updated_at: 2024-04-03 00:44:02 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1548784415  

Are the number of vertices in G1 and G2 guaranteed to be equal here?   
  
If not, then one minor optimization we can do is to check that the number of vertices in both graphs is equal before going ahead with creating the arrays and sorting them, etc.

> Username: jan-grimo  
> Created_at: 2024-04-11 19:54:30 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1561573224  

Yes, this is guaranteed by [an earlier check](https://github.com/boostorg/graph/blob/b737cca58ae6d31cdd4241ff4de7b4755ba614a7/include/boost/graph/isomorphism.hpp#L596).


---

## Review 14 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-04-03 01:04:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1975312277  

📁 include/boost/graph/isomorphism.hpp

```diff
 199 |-                     V_mult, compare_multiplicity(invariant1, &multiplicity[0]));
 200 |-             }
 220 |+             sort(V_mult, compare_multiplicity(invariant1, multiplicities(invar1_array.begin(), invar1_array.end())));
```

> Username: jeremy-murphy  
> Created_at: 2024-04-03 01:04:56 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1548793314  

You don't need this `multiplicities` function, and I don't want us to commit the design to an associative container just yet.  
  
Declare the container first and then just use `std::for_each`, like so:  
  
```  
multiplicity_map multiples;  
std::for_each(invar1_array.begin(), invar1_array.end(), [&](auto invariant){ multiples[invariant]++; });  
sort(V_mult, compare_multiplicity(invariant1, multiples));  
```

> Username: jan-grimo  
> Created_at: 2024-04-11 20:01:51 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1561580979  

Are you sure? We discussed the performance merits of using the `multiplicities` function [here](https://github.com/boostorg/graph/pull/349#discussion_r1324068860) before.

> Username: jeremy-murphy  
> Created_at: 2024-04-11 21:12:27 UTC  
> Updated_at: 2024-04-11 21:12:28 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1561746901  

Thanks for the reminder.  I have to laugh at my memory's frailty.  :)


---

## Review 15 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-04-03 01:05:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1975312527  

📁 include/boost/graph/isomorphism.hpp

```diff
 192 |+             return invar_multiplicity;
 193 |+         }
 194 |+ 
```

> Username: jeremy-murphy  
> Created_at: 2024-04-03 01:05:21 UTC  
> Updated_at: 2024-04-03 01:05:22 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1548793469  

As I mentioned in another comment, I think you can delete this function.


---

## Review 16 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-04-05 05:36:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1981977560  

📁 include/boost/graph/isomorphism.hpp

```diff
  43 |-         typedef typename Invariant2::result_type invar2_value;
  42 |+         typedef typename Invariant1::result_type invariant_t;
  43 |+         typedef unordered_map< invariant_t, size_type > multiplicity_map;
```

> Username: jeremy-murphy  
> Created_at: 2024-04-05 05:36:38 UTC  
> Updated_at: 2024-04-05 05:37:46 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1552952565  

It's important to let the user choose this type, so it needs to become a template parameter with a default value, like:  
```  
    template < typename Graph1, typename Graph2, typename IsoMapping,  
        typename Invariant1, typename Invariant2, typename IndexMap1,  
        typename IndexMap2, typename InvariantCountMap = boost::unordered_flat_map< typename Invariant1::result_type, typename graph_traits< Graph1 >::vertices_size_type > >  
```  
Note that I've used `boost::unordered_flat_map`, which generally blows `boost::unordered_map` out of the water. (See [here](https://www.boost.org/doc/libs/develop/libs/unordered/doc/html/unordered.html).)

> Username: jeremy-murphy  
> Created_at: 2024-04-11 21:36:52 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1561780257  

This is the only change left, then it's good to merge!


---

## Review 17 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-04-05 05:39:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/349#pullrequestreview-1981979950  

📁 include/boost/graph/isomorphism.hpp

```diff
  92 |+                 auto y_multiplicity_iter = multiplicity->find(invariant1(y));
  93 |+                 assert(y_multiplicity_iter != multiplicity->end());
  94 |+                 return *x_multiplicity_iter < *y_multiplicity_iter;
```

> Username: jeremy-murphy  
> Created_at: 2024-04-05 05:39:10 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1552954198  

I'd like to revert this back to the original code and add a TODO comment about a generic assertion that the map already contains the elements.

> Username: jeremy-murphy  
> Created_at: 2024-04-11 21:32:55 UTC  
> Url: https://github.com/boostorg/graph/pull/349#discussion_r1561775783  

Actually, don't worry about this after all. Apologies if you have already started working on the change.


---

## Comment 18

> Username: jeremy-murphy  
> Created_at: 2024-04-08 05:16:06 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2041880729  

@jan-grimo , do you still have time to finish this off? I'd like to get it done before I merge `develop` into `master`. I think there's just a bit of minor editing to be done.

---

## Comment 19

> Username: jan-grimo  
> Created_at: 2024-04-12 18:53:13 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2052321485  

I don't understand what's wrong :see_no_evil:  
  
```  
2024-04-12T16:13:34.8397092Z D:\a\graph\boost-root\boost/unordered/detail/foa/core.hpp(1284): error C2146: syntax   
error: missing '>' before identifier 'key_type'  
2024-04-12T16:13:34.8410724Z D:\a\graph\boost-root\boost/unordered/detail/foa/core.hpp(1289): note: see reference to class template instantiation 'is_map<Container>' being compiled  
2024-04-12T16:13:34.8528000Z   
```

---

## Comment 20

> Username: jeremy-murphy  
> Created_at: 2024-04-12 21:19:54 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2052546373  

> I don't understand what's wrong 🙈  
>   
> ```  
> 2024-04-12T16:13:34.8397092Z D:\a\graph\boost-root\boost/unordered/detail/foa/core.hpp(1284): error C2146: syntax   
> error: missing '>' before identifier 'key_type'  
> 2024-04-12T16:13:34.8410724Z D:\a\graph\boost-root\boost/unordered/detail/foa/core.hpp(1289): note: see reference to class template instantiation 'is_map<Container>' being compiled  
> 2024-04-12T16:13:34.8528000Z   
> ```  
  
Strange, I don't know either.  
  
It may simply be that MSVC 14.0 is broken and I should take it out of the list.

---

## Comment 21

> Username: jeremy-murphy  
> Created_at: 2024-04-13 21:25:18 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2053765081  

I removed it, so please merge develop into your branch and try again.

---

## Comment 22

> Username: jeremy-murphy  
> Created_at: 2024-04-15 01:19:35 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2054271322  

Thanks for persisting with it, @jan-grimo !

---

## Comment 23

> Username: jan-grimo  
> Created_at: 2024-04-15 05:44:57 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2055392963  

You're welcome! Thank you for helping me get it over the line @jeremy-murphy

---

## Comment 24

> Username: jeremy-murphy  
> Created_at: 2024-05-29 00:47:31 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2136324476  

Are you able to quantify how the space efficiency changed? I mean, previously it was allocating a sequence container (vector) of size `max_invariant`, and now it allocates an associative container (hash map) of the same size as G1 -- did that change the overall space efficiency? Can you describe under what conditions the improved space efficiency occurs? I mean, what would previously cause `max_invariant` to be much greater than the size of G1 and thus incur a large allocation?  
  
I just want to accurately document the change in the release notes. Thanks!

---

## Comment 25

> Username: jan-grimo  
> Created_at: 2024-05-29 05:53:41 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2136570749  

Space efficiency hasn't changed for the general use case without custom invariants or the already feasible approaches with custom invariants that output a contiguous or small output domain on 0..N. But now arbitrary vertex invariants over vertex properties are feasible without incurring enormous memory allocations.  
  
I had a use case with many vertex properties where I had set up a perfect hash function as an invariant, but then ran into the hidden memory allocation, which in the case of a 64-bit hash caused an OOM abort. I circumvented it by mapping the hashes for both graphs from their distribution over 0..2^64-1 down onto 0..N (where N is the number of unique hashes for the two graphs being compared). But I was irked by the hidden allocation and the odd invariant functor interface and thought it could be easier, hence the MRs that came from #203.

---

## Comment 26

> Username: jeremy-murphy  
> Created_at: 2024-05-30 23:45:20 UTC  
> Url: https://github.com/boostorg/graph/pull/349#issuecomment-2141010764  

Cool, thanks.

---
