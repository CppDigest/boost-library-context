# #26 Query params operations [Closed]

> Username: zajo  
> Created at: 2021-01-30 02:26:38 UTC  
> Updated at: 2021-09-14 04:55:01 UTC  
> Closed at: 2021-09-14 04:55:01 UTC  
> Url: https://github.com/boostorg/url/pull/26  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 03:24:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579724032  

📁 include/boost/url/impl/url_base.ipp

```diff
1658 |-     nk_ = p - p0;
1659 |-     if(p == end)
1657 |+     if(p != end && *p != '=' && *p != '&')
```

> Username: vinniefalco  
> Created_at: 2021-01-30 03:24:10 UTC  
> Updated_at: 2021-01-30 19:58:03 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567189078  

it helps coverage to break these things up on individual lines:  
```  
if( p != end &&  
    *p != '=' &&  
    *p != '&')  
```  
  
This way we know that each condition is covered


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 03:24:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579724061  

📁 include/boost/url/impl/url_base.ipp

```diff
1753 |+ url_base::
1754 |+ params_type::
1755 |+ erase( url_base::params_type::iterator pos ) noexcept ->
```

> Username: vinniefalco  
> Created_at: 2021-01-30 03:24:39 UTC  
> Updated_at: 2021-01-30 19:58:03 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567189114  

Why the full qualification?


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 03:25:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579724099  

📁 include/boost/url/impl/url_base.ipp

```diff
1795 |+     BOOST_ASSERT(v.s_[v.pt_.offset[detail::id_end]] == '\0');
1796 |+     first.parse();
1797 |+     return first;
```

> Username: vinniefalco  
> Created_at: 2021-01-30 03:25:13 UTC  
> Updated_at: 2021-01-30 19:58:03 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567189156  

For some reason this block of code seems overly verbose


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 03:25:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579724146  

📁 include/boost/url/impl/url_base.ipp

```diff
1801 |+ url_base::
1802 |+ params_type::
1803 |+ insert_encoded_impl( params_type::iterator pos, string_view pk, string_view pv ) ->
```

> Username: vinniefalco  
> Created_at: 2021-01-30 03:25:47 UTC  
> Updated_at: 2021-01-30 19:58:03 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567189254  

the names for these parameters should be `k` and `v`


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-01-30 03:26:35 UTC  
> Url: https://github.com/boostorg/url/pull/26#issuecomment-770148289  

Where are the `emplace` overloads?

---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:01:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579826852  

📁 include/boost/url/impl/url_base.ipp

```diff
1970 |+ {
1971 |+     (void) detail::pchar_pct_set().validate(p.k_);
1972 |+     (void) detail::pchar_pct_set().validate(p.v_);
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:01:59 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567332008  

wouldn't need (void) if we didn't use nodiscard.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:02:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579826866  

📁 include/boost/url/impl/url_base.ipp

```diff
1978 |+     auto const nv = p.v_.size();
1979 |+     auto const n0 = pos.nk_ + pos.nv_;
1980 |+     auto const n = 1 + nk + (nv!=0) + nv; // Delimiter, key, =, value
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:02:13 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567332126  

please put the comment on the previous line


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:03:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579826921  

📁 include/boost/url/impl/url_base.ipp

```diff
1992 |+ 
1993 |+     // If the reserve was exceeded, we have a strong guarantee violation
1994 |+     BOOST_ASSERT(v.a_.capacity() == cap);
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:03:20 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567332616  

I don't see the point to checking this, it is extra noise


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:04:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579826974  

📁 include/boost/url/impl/url_base.ipp

```diff
2005 |+ {
2006 |+     BOOST_ASSERT(v_ != nullptr);
2007 |+     url_base & v = *v_;
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:04:16 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567333116  

`url_base&` please


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:05:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579827032  

📁 include/boost/url/impl/url_base.ipp

```diff
2012 |+     auto const nv = pct.encoded_size(p.v_);
2013 |+     auto const n0 = pos.nk_ + pos.nv_;
2014 |+     auto const n = 1 + nk + (nv!=0) + nv; // Delimiter, key, =, value
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:05:38 UTC  
> Updated_at: 2021-01-30 23:06:16 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567333710  

please put the comment on the previous line, alternatively:  
```  
 auto const n =  
     1 +                 // '&'  
     nk +                // key  
     ( nv !=0  ) +       // '='  
     nv;                 // value  
```


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:06:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579827092  

📁 include/boost/url/impl/url_base.ipp

```diff
2025 |+         nk,
2026 |+         p.v_,
2027 |+         nv);
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:06:50 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567333839  

good format


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:09:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579827215  

📁 include/boost/url/impl/url_base.ipp

```diff
2029 |+     // If the reserve was exceeded, we have a strong guarantee violation
2030 |+     BOOST_ASSERT(v.a_.capacity() == cap);
2031 |+     (void) cap;
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:09:12 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567334116  

I think the verbosity and awkward code flow stems from the fact that we are calling a throwing function `insert_impl` from other functions which have effects and postconditions (e.g. strong guarantee).  
  
We could simply make it a requirement that the caller reserves the necessary space, and then `insert_impl` would merely assert that the capacity is sufficient. Then we won't need to have these local variables which are not used in release builds and we won't need these unnecessary asserts.


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:10:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579827271  

📁 include/boost/url/url_base.hpp

```diff
2005 |+     BOOST_URL_DECL
2006 |+     iterator
2007 |+     erase( iterator b, iterator e ) noexcept;
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:10:15 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567334231  

The canonical formal parameter names are `first` and `last`. Yes I realize that "last" is a bit misleading but this is better than "b & e".


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:15:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579827507  

📁 test/url.cpp

```diff
 865 |+                         qp.insert_encoded(
 866 |+                             qp.insert_encoded(qp.end(), {"e", "5"} ), {"d", "4"} ), {"c", "3"} ), {"b", "2"} ), {"a", "1"} );
 867 |+             BOOST_TEST(!qp.empty());
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:15:34 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567334616  

The tests should exercise each member function in their order of appearance in the header file. If this order is ever changed, the tests need to be refactored as well. Each test should mention the function in a comment.  
  
See:   
https://github.com/boostorg/json/blob/develop/test/object.cpp#L1050  
  
And then we check the coverage report to make sure that every code path is tested.  
  
I'll note that your test does too much - inserting 5 times should not be needed. You should calculate all of the meaningful permutations of insertions. Example:  
  
Append A to {}  
Append B to { A }  
Insert A to { B } before B  
Insert B to { A, C } before C  
  
and so on. Do each with a non-empty value and an empty value. And encoded versus decoded. So this is a total of 16 tests for `insert`. Each one should be labeled with a comment, like this:  
  
https://github.com/boostorg/json/blob/develop/test/object.cpp#L961  
  
For above, the permutations are "small", "small, existing duplicate", "small, new duplicate" and so forth, for the function with signature `object::insert( initializer_list<...> )` (line 959)  
  
Also note the use of braces to isolate the scope of each test.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-30 23:17:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/26#pullrequestreview-579827586  

📁 test/url.cpp

```diff
 886 |+             BOOST_TEST(qp.size() == 4);
 887 |+             BOOST_TEST(std::distance(qp.begin(), qp.end()) == 4);
 888 |+             BOOST_TEST(u.encoded_query() == "a=1&c=3&d=4&e=5");
```

> Username: vinniefalco  
> Created_at: 2021-01-30 23:17:01 UTC  
> Updated_at: 2021-01-30 23:17:12 UTC  
> Url: https://github.com/boostorg/url/pull/26#discussion_r567334783  

we need to know what happens with degenerate query strings. For example what if the query string is  
`?&`  
`?&&`  
`?=`  
`?=&`  
`?&=`  
`?&=&`  
  
And you try to insert (for each case)?


---

## Comment 16

> Username: vinniefalco  
> Created_at: 2021-09-14 04:55:01 UTC  
> Url: https://github.com/boostorg/url/pull/26#issuecomment-918800961  

This code has been refactored and no longer resembles the previous version

---
