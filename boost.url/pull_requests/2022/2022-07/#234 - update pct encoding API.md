# #234 update pct encoding API [Merged]

> Username: alandefreitas  
> Created at: 2022-07-19 01:42:26 UTC  
> Updated at: 2022-08-03 21:07:02 UTC  
> Merged at: 2022-07-19 20:40:08 UTC  
> Closed at: 2022-07-19 20:40:08 UTC  
> Url: https://github.com/boostorg/url/pull/234  

fix #230  
  
This PR fixes the issue with the default parameters for pct encoding algorithms and updates the API according to the other recommendations in #230   
  
It would be good to have a good look at this because this has an impact at many other points of the library.  
  
I'm still not sure what to do about the recommendation "add `pct_encode_append_to` and `pct_encode_assign_to`" because the functions `append` and `assign` depend on iterators which don't exist in that context:  
  
- If the intention is to reuse the MutableString concept, the first alternative would be to create a whole new view class representing the opposite of `pct_encoded_view` (received decoded strings as input and outputing encoded strings) so we have iterators to provide `append` and `assign`.   
- If the intention is really to create a new concept, the second alternative is to create a new concept including MutableString _and_ `resize` in the recycled class.   
- The third alternative is, of course, only accepting `basic_string`. The we know we have `resize`, `assign` and `append`.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:18:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042740047  

📁 include/boost/url/detail/impl/any_query_iter.ipp

```diff
 154 |-     n += urls::pct_encode_bytes(
 155 |-         s, {}, query_chars);
 154 |+     n += urls::pct_encode_bytes(s, query_chars);
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:18:48 UTC  
> Updated_at: 2022-07-19 02:18:49 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924000265  

This line sticks out


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:23:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042742343  

📁 include/boost/url/impl/pct_encoding.hpp

```diff
 311 |     pct_encode_opts const& opt,
 384 |-     CharSet const& cs,
 312 |     Allocator const& a)
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:23:25 UTC  
> Updated_at: 2022-07-19 02:23:26 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924001872  

no default Allocator?

> Username: alandefreitas  
> Created_at: 2022-07-19 03:10:47 UTC  
> Updated_at: 2022-07-19 03:10:48 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924019639  

The default parameter is in the declaration. This is the implementation file.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:24:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042742672  

📁 include/boost/url/impl/pct_encoding.ipp

```diff
  25 |-     auto const end =
  26 |-         it + s.size();
  25 |+     auto const end = it + s.size();
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:24:04 UTC  
> Updated_at: 2022-07-19 02:24:05 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924002095  

why did you reformat this line?


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:24:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042742900  

📁 include/boost/url/impl/pct_encoding.ipp

```diff
 133 | }
 134 | 
 135 |+ BOOST_URL_DECL
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:24:27 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924002264  

I think, you don't put `BOOST_URL_DECL` on the definition?

> Username: alandefreitas  
> Created_at: 2022-07-19 03:11:45 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924019939  

Ooops... bad copying and pasting.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:25:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042743607  

📁 include/boost/url/impl/pct_encoding.ipp

```diff
 153 |+     {
 154 |+         if (!opt.allow_null &&
 155 |+             *it == '\0')
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:25:56 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924002838  

Should you check `*it` first? Otherwise you are doing both comparisons _every_ time for the common case.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:27:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042744223  

📁 include/boost/url/impl/pct_encoding.ipp

```diff
 149 |+     char const* it = s.data();
 150 |+     char const* end = it + s.size();
 151 |+     it = grammar::find_if_not(it, end, is_safe);
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:27:09 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924003340  

`allow_null` can't change during the find loop but you are checking it every time


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:28:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042744990  

📁 include/boost/url/pct_encoding.hpp

```diff
  95 | 
  96 |-     @param cs An optional character set to use.
  96 |+     @param allowed An optional character set to use.
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:28:42 UTC  
> Updated_at: 2022-07-19 02:29:28 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924003869  

it isn't optional. This should read something like "The set of characters allowed  to appear unescaped..." You should use the word "allowed" in the sentence.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:29:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042745602  

📁 include/boost/url/pct_encoding.hpp

```diff
 122 |+     pct_decode_opts const& opt = {}) noexcept;
 123 |+ 
 124 |+ /** Validate a percent encoded string and return the number of decoded bytes
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:29:56 UTC  
> Updated_at: 2022-07-19 02:30:15 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924004277  

"Return the decoded size of a percent-encoded string, with validation"  
  
Functions that return values should have briefs that start with the word "Return" when possible


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:32:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042746904  

📁 include/boost/url/pct_encoding.hpp

```diff
 297 |+     occurred. If the destination buffer
 298 |+     is too small to hold the result, `ec`
 299 |+     is set to @ref error::no_space.
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:32:35 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924005316  

You shouldn't go down to the level of detail of specifying the error for trivial things, unless the error is something that happens on a regular basis (for example: asio's `error::eof` when the socket is closed). Because if we change it, then we have to change the doc. And most users won't see this error anyway. And the ones that do see the error, they can look it up and they will know what it means.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 02:32:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1042747067  

📁 include/boost/url/pct_encoding.hpp

```diff
 472 | 
 517 |-     @param cs The character set to use.
 473 |+     @param allowed The character set to use.
```

> Username: vinniefalco  
> Created_at: 2022-07-19 02:32:56 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924005447  

This has to be explained you can't just say "the character set"


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 16:49:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/234#pullrequestreview-1043785937  

📁 include/boost/url/detail/impl/any_query_iter.ipp

```diff
 254 |-         n += pct_encode_bytes(
 255 |-             *value, {}, query_chars);
 252 |+         n += pct_encode_bytes(*value, query_chars);
```

> Username: vinniefalco  
> Created_at: 2022-07-19 16:49:39 UTC  
> Url: https://github.com/boostorg/url/pull/234#discussion_r924740782  

This line sticks out now


---

## Comment 12

> Username: vinniefalco  
> Created_at: 2022-07-19 16:57:55 UTC  
> Url: https://github.com/boostorg/url/pull/234#issuecomment-1189334115  

Looks good lets merge it

---
