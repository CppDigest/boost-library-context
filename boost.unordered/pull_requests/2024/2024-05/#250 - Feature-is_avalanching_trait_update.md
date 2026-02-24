# #250 Feature/is_avalanching_trait_update [Merged]

> Username: joaquintides  
> Created at: 2024-05-29 18:28:52 UTC  
> Updated at: 2024-05-30 18:04:24 UTC  
> Merged at: 2024-05-30 18:04:19 UTC  
> Closed at: 2024-05-30 18:04:19 UTC  
> Url: https://github.com/boostorg/unordered/pull/250  

Fixes #248.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-05-29 18:32:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/250#pullrequestreview-2086135156  

📁 doc/unordered/hash_traits.adoc

```diff
  39 |+  * `true` if `Hash::is_avalanching` is `void` (this usage is deprecated).
  40 |+  
  41 |+ The behavior is undefined if none of the three cases above is met.
```

> Username: pdimov  
> Created_at: 2024-05-29 18:32:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619320291  

I don't think we want "undefined" here.

> Username: joaquintides  
> Created_at: 2024-05-29 18:42:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619330539  

You're right, I miswrote that for "ill-formed". What's specifically ill formed? The "program", the expression `hash_is_avalanching<Hash>::value`, something else?

> Username: pdimov  
> Created_at: 2024-05-29 19:08:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619357570  

The program, I suppose.

> Username: joaquintides  
> Created_at: 2024-05-29 19:27:00 UTC  
> Updated_at: 2024-05-29 19:27:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619375918  

I think I'll go with "the expression", as the user can legitimately define and use something like:  
```cpp  
struct my_hash{ using is_avalanching=int; };  
```  
as long as he doesn't plug into Boost.Unordered.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2024-05-29 18:34:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/250#pullrequestreview-2086138001  

📁 doc/unordered/hash_traits.adoc

```diff
  35 |+ `hash_is_avalanching<Hash>::value` is:
  36 |+ 
  37 |+  * `false` if `Hash::is_avalanching` is not present,
```

> Username: pdimov  
> Created_at: 2024-05-29 18:34:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619321821  

What do we want when `is_avalanching` is present, but is not a type? `false` or a compile error? The latter, probably.

> Username: joaquintides  
> Created_at: 2024-05-29 18:46:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619334353  

Now it gives `false`.  How do we make that compile-fail?

> Username: joaquintides  
> Created_at: 2024-05-29 18:51:54 UTC  
> Updated_at: 2024-05-29 18:51:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619340384  

We can add a specialization like this:  
```cpp  
template<typename Hash>  
struct hash_is_avalanching_impl<  
  Hash,  
  typename std::enable_if<((void)Hash::is_avalanching,true)>::type  
>{};  
```

> Username: pdimov  
> Created_at: 2024-05-29 19:09:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619359167  

Let's just document it as `false` and be done with it. It's way too convoluted otherwise.

> Username: joaquintides  
> Created_at: 2024-05-29 19:28:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619377051  

Maybe we _should_ make it a compile-time error to protect users from:  
```cpp  
struct my_hash  
{  
  static constexpr bool is_avalanching=true;  
};  
```

> Username: pdimov  
> Created_at: 2024-05-29 19:31:51 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619380730  

That's exactly what I thought, but if it's too much of a hassle, we shouldn't go out of our way to try and catch that.

> Username: joaquintides  
> Created_at: 2024-05-29 19:57:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1619407349  

It's not a hassle, the change proposed above does indeed do the trick.

> Username: cmazakas  
> Created_at: 2024-05-30 15:41:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#discussion_r1620981857  

Fwiw, if Joaquin doesn't mind putting in the work, I also think making it a compiler error would be fantastic. As a user of Unordered, this is exacly the behavior I'd want (i.e. saving me from myself).


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-05-29 18:37:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#issuecomment-2138035403  

An automated preview of the documentation is available at [https://250.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://250.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-05-30 07:22:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/250#issuecomment-2138850677  

An automated preview of the documentation is available at [https://250.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://250.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
