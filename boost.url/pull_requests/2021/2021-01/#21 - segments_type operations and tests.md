# #21 segments_type operations and tests [Closed]

> Username: zajo  
> Created at: 2021-01-25 21:05:10 UTC  
> Updated at: 2021-01-27 00:47:54 UTC  
> Closed at: 2021-01-27 00:47:54 UTC  
> Url: https://github.com/boostorg/url/pull/21  

Need more comprehensive tests and documentation, but operational.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:11:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575884859  

📁 include/boost/url/detail/storage.hpp

```diff
  68 |     {
  64 |-         if(n <= cap_)
  69 |+         if(n > cap_)
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:11:56 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564106279  

The reason I use two `return` statements is it makes the coverage report better. The way you have it written, there is no way to know that the case where `n <= cap_` is taken.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:13:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575885467  

📁 include/boost/url/impl/url_base.hpp

```diff
  55 | 
  56 |+     using pointer =
  57 |+         value_type *;
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:13:16 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564106759  

Since lines 50 and 51 are already as wide as they are there's no need to put `value_type*` on a separate line.  
  
Should this be `value_type const*`?

> Username: zajo  
> Created_at: 2021-01-25 23:39:02 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564116561  

I'll gladly change it, but you'll have to continue to point these one by one, this makes no sense to me.

> Username: vinniefalco  
> Created_at: 2021-01-25 23:47:38 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564119557  

For the formatting I don't care enough to want it to be changed, but what about `value const*`?

> Username: zajo  
> Created_at: 2021-01-26 00:36:18 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564135902  

Oh I missed that, sorry. Yes, also `reference` should be `value_type const &` right?

> Username: vinniefalco  
> Created_at: 2021-01-26 00:42:36 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564138037  

yeah but if you could put `const&` together instead of `const &` (and ditto for `const*`) it would be great

> Username: zajo  
> Created_at: 2021-01-26 00:48:30 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564139916  

Sure. Should I replace it elsewhere too? I may have put spaces before `*` and `&` in other places.

> Username: vinniefalco  
> Created_at: 2021-01-26 01:17:45 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564149190  

yes please


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:13:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575885730  

📁 include/boost/url/impl/url_base.ipp

```diff
1341 |+ url_base::
1342 |+ segments_type::
1343 |+ erase( url_base::segments_type::iterator pos ) noexcept ->
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:13:51 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564106972  

I believe you can simply write `iterator pos` instead of `url_base::segments_type::iterator pos`

> Username: zajo  
> Created_at: 2021-01-25 23:40:44 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564117196  

Indeed.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:15:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575886522  

📁 include/boost/url/impl/url_base.ipp

```diff
1355 |+ {
1356 |+     BOOST_ASSERT(v_ != nullptr);
1357 |+     url_base & v = *v_;
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:15:39 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564107636  

why `v` instead of `v_`?

> Username: zajo  
> Created_at: 2021-01-25 23:55:03 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564122258  

v_ is really this->v_. Consider:  
  
```  
struct X { int y; };  
  
void f();  
  
void use( int );  
  
...  
  
void g( X * x )  
{  
    use(x->y); // read x->y from memory.  
    f();  
    use(x->y); // x->y is read from memory a second time because f may have changed it through its own pointer.  
}  
```  
  
Compare to:  
  
```  
void g( X * x )  
{  
    int y = x->y;  
    use(y);  
    f();  
    use(y); // no need to read x->y again even if it has changed (we know it hasn't).  
}  
```  
  
The idea is to tell the compiler that `v` will not ever point a different object (because it is local and other code has no access to it and therefore can't change it, whereas `v_` may be changed through `this`).

> Username: vinniefalco  
> Created_at: 2021-01-26 00:43:28 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564138282  

Okay that sounds convincing but please put `&` up against the type:  
```  
url_base& v = *v_;  
```


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:16:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575886916  

📁 include/boost/url/impl/url_base.ipp

```diff
1369 |+     int c = 0;
1370 |+     for( auto i = v.s_ + first.off_, e = v.s_ + last.off_; i != e; ++i )
1371 |+         c += (*i == '/');
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:16:37 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564107961  

You should put a comment here to explain what the loop does. My guess is that it counts the number of segments between `first` and `last`.

> Username: zajo  
> Created_at: 2021-01-25 23:57:08 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564123044  

Good guess. I'll add the comment.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:17:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575887110  

📁 include/boost/url/impl/url_base.ipp

```diff
1385 |+ url_base::
1386 |+ segments_type::
1387 |+ insert_encoded_segment_( segments_type::iterator pos, string_view s ) ->
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:17:01 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564108097  

instead of a trailing underscore, use the suffix `_impl` to denote that a function is a private implementation function


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:18:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575887634  

📁 include/boost/url/impl/url_base.ipp

```diff
1472 |+     auto const cap = v.a_.capacity();
1473 |+     auto r = insert_encoded_segment_(erase(pos), s);
1474 |+     BOOST_ASSERT(v.a_.capacity() == cap); // Strong guarantee violation
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:18:03 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564108605  

`capacity` does not have to be preserved on exception

> Username: zajo  
> Created_at: 2021-01-26 00:01:08 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564124491  

You're not reading this correctly. We reserve first, this assert verifies that `insert_encoded_segment_` did not need to expand the memory, which would mean that it could fail, which would violate the strong guarantee.

> Username: vinniefalco  
> Created_at: 2021-01-26 17:38:49 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564702443  

the coverage report will have no way to indicate that both paths of the `if` were covered


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:18:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575888011  

📁 include/boost/url/impl/url_base.ipp

```diff
1449 |+ url_base::
1450 |+ segments_type::
1451 |+ insert_segment( segments_type::iterator pos, string_view s ) ->
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:18:52 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564108943  

do we need the suffix `_segment` on every function? It seems redundant

> Username: zajo  
> Created_at: 2021-01-26 00:33:07 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564134939  

There is encoded/not encoded. Do you prefer `insert`/`insert_encoded`? I guess the longer version is more novice-friendly. Want me to change it?

> Username: vinniefalco  
> Created_at: 2021-01-26 00:44:04 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564138468  

I think having to type `_segment` every time is burdensome. Note that _every_ function you added has it, which suggests it is redundant.

> Username: zajo  
> Created_at: 2021-01-26 00:48:50 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564140039  

Agreed. Changing it.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:19:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575888508  

📁 include/boost/url/url_base.hpp

```diff
1702 |+     BOOST_URL_DECL
1703 |+     iterator
1704 |+     replace_segment( iterator pos, string_view s );
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:19:56 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564109350  

All these functions need javadocs. The best time to write the reference documentation is when you implement the function since it will be fresh in your head. Coming back to it weeks or months later and you might forget things.

> Username: zajo  
> Created_at: 2021-01-26 00:02:12 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564124789  

Yes all functions need to be documented. I prefer if you approve the interface before I write the documentation so I don't have to document things that get rejected.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-26 01:55:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-575951465  

📁 include/boost/url/impl/url_base.hpp

```diff
  56 |+     using pointer = value_type const*;
  57 |+     using reference = value_type const&;
  58 |+     using difference_type = std::ptrdiff_t;
```

> Username: vinniefalco  
> Created_at: 2021-01-26 01:55:06 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564163139  

These need javadocs


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-26 17:29:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576589417  

📁 include/boost/url/impl/url_base.hpp

```diff
  54 |         segments_type::value_type;
  55 | 
  56 |+     /** The pointer type of segments_type::iterator.
```

> Username: vinniefalco  
> Created_at: 2021-01-26 17:29:43 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564696254  

Why do you keep repeating this `segments_type::`?


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-26 17:30:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576589756  

📁 include/boost/url/impl/url_base.hpp

```diff
  60 |+         @code
  61 |+         using pointer = value_type const*;
  62 |+         @endcode
```

> Username: vinniefalco  
> Created_at: 2021-01-26 17:30:07 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564696517  

There's no need for this "Define as", the doc toolchain automatically repeats the declaration.

> Username: vinniefalco  
> Created_at: 2021-01-26 17:30:42 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564696927  

Example:   
![image](https://user-images.githubusercontent.com/1503976/105881284-2805f680-5fb9-11eb-9741-a095b7d35f1d.png)


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-26 17:31:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576590969  

📁 include/boost/url/impl/url_base.hpp

```diff
  81 |+         using difference_type = std::ptrdiff_t;
  82 |+         @endcode
  83 |+     */
```

> Username: vinniefalco  
> Created_at: 2021-01-26 17:31:24 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564697458  

Please copy the javadoc briefs (the first line of the javadoc) from the containers in JSON  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__object.html


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-26 17:33:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576592677  

📁 include/boost/url/url_base.hpp

```diff
1727 |+         @param s The unencoded path segment to be inserted.
1728 |+ 
1729 |+         @throws std::bad_alloc
```

> Username: vinniefalco  
> Created_at: 2021-01-26 17:33:20 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564698735  

We never document `bad_alloc` explicitly (unless we are documenting an allocator). Instead we simply write:  
  
> Strong guarantee. Calls to allocate may throw.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-26 17:34:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576593523  

📁 include/boost/url/url_base.hpp

```diff
1763 |+         @par Exception Safety
1764 |+ 
1765 |+         Strong guarantee.
```

> Username: vinniefalco  
> Created_at: 2021-01-26 17:34:22 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564699413  

Please follow the order of directives used in JSON:  
1. exception safety,  
2. exceptions  
3. parameters  
  
e.g.  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__object/insert/overload2.html


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-26 17:37:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576595887  

📁 include/boost/url/impl/url_base.ipp

```diff
1368 |+     BOOST_ASSERT(d > 0);
1369 |+     int c = 0;
1370 |+     for( auto i = v.s_ + first.off_, e = v.s_ + last.off_; i != e; ++i )
```

> Username: vinniefalco  
> Created_at: 2021-01-26 17:37:06 UTC  
> Updated_at: 2021-01-26 23:07:16 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564701231  

```  
// Count the number of segments in the range  
```  
  
Should iterators keep an ordinal of where they are?


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-27 00:18:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576874952  

📁 include/boost/url/url_base.hpp

```diff
1617 | 
1618 |- /** A read-only view to the path segments.
1618 |+ /** A read/write view to the path segments.
```

> Username: vinniefalco  
> Created_at: 2021-01-27 00:18:14 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564928395  

"A mutable view to the path segments"


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-27 00:19:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576875309  

📁 include/boost/url/url_base.hpp

```diff
1630 |+ private:
1631 |+ 
1632 |+     // Strong guarantee.
```

> Username: vinniefalco  
> Created_at: 2021-01-27 00:19:10 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564928736  

For private members I put these types of comments on the definition rather than the declaration. A minor point.


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-27 00:19:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576875436  

📁 include/boost/url/url_base.hpp

```diff
1676 |     end() const noexcept;
1677 |+ 
1678 |+     /** Erase the specified sequence of path segments.
```

> Username: vinniefalco  
> Created_at: 2021-01-27 00:19:27 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564928844  

"Erase a range of segments"


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-27 00:20:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576875738  

📁 include/boost/url/url_base.hpp

```diff
1694 |+         @param pos The path segment to erase
1695 |+ 
1696 |+         This function is equivalent to:
```

> Username: vinniefalco  
> Created_at: 2021-01-27 00:20:12 UTC  
> Updated_at: 2021-01-27 00:20:13 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564929129  

Telling the user what it is equivalent to in terms of some other function isn't as useful as actually just saying what it does. And you also need to say something about iterator invalidation.


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-27 00:22:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/21#pullrequestreview-576876605  

📁 test/url.cpp

```diff
 710 |+                 BOOST_TEST(u.encoded_path() == "/" + s + "%2F/a/b/c/d/file.txt");
 711 |+ 
 712 |+                 ps.replace_encoded(ps.begin(), "%2F");
```

> Username: vinniefalco  
> Created_at: 2021-01-27 00:22:22 UTC  
> Url: https://github.com/boostorg/url/pull/21#discussion_r564929922  

after the replacement dont you need to test the result?


---

## Comment 22

> Username: vinniefalco  
> Created_at: 2021-01-27 00:26:50 UTC  
> Url: https://github.com/boostorg/url/pull/21#issuecomment-767919710  

Please squash it all down

---
