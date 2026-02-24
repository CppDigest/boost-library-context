# #136 syntax-based normalization [Closed]

> Username: alandefreitas  
> Created at: 2022-03-04 04:38:34 UTC  
> Updated at: 2022-03-11 19:37:56 UTC  
> Closed at: 2022-03-11 19:37:56 UTC  
> Url: https://github.com/boostorg/url/pull/136  

fix #8, fix #65

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 04:52:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899802419  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 204 |+         // the input buffer.
 205 |+         std::size_t p = s.find_first_of('/', 1);
 206 |+         if (p != std::size_t(-1))
```

> Username: vinniefalco  
> Created_at: 2022-03-04 04:52:39 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819263321  

`string_view::npos` instead of `std::size_t(-1)`


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 04:54:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899803136  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 308 |+             std::size_t& n_skip)
 309 |+     {
 310 |+         child = string_view{};
```

> Username: vinniefalco  
> Created_at: 2022-03-04 04:54:22 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819263892  

prefer `child = {};`  
Rationale: less typing instead of more


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 04:55:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899803687  

📁 include/boost/url/detail/remove_dot_segments.hpp

```diff
  18 |+ namespace detail {
  19 |+ 
  20 |+ /** Return a string with remove_dot_segments applied.
```

> Username: vinniefalco  
> Created_at: 2022-03-04 04:55:44 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819264416  

we don't put full javadocs on non-public interfaces..

> Username: alandefreitas  
> Created_at: 2022-03-05 15:44:15 UTC  
> Updated_at: 2022-03-05 21:08:09 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820117809  

I thought this was going to be public at first so I wrote the javadocs. So I didn't see the point in removing it when I made it a detail. Some other functions in detail, such as key_equal_encoded, also have javadocs. I assumed they were a similar story.

> Username: vinniefalco  
> Created_at: 2022-03-05 21:37:43 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820150458  

There are reasons to remove it. First, I don't make the mistake of reviewing it needlessly since it looks public. Second, we don't have to maintain it when it changes. And third, since it is removed it can never become out of sync.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 04:58:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899804767  

📁 include/boost/url/impl/url_view.ipp

```diff
 454 |+ compare(const url_view& other) const noexcept
 455 |+ {
 456 |+     int comp = detail::pct_decode_icompare_unchecked(
```

> Username: vinniefalco  
> Created_at: 2022-03-04 04:58:19 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819265281  

the scheme cannot contain percent-encoded characters:  
  
```  
scheme      = ALPHA *( ALPHA / DIGIT / "+" / "-" / "." )  
```


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 05:03:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899806969  

📁 include/boost/url/url.hpp

```diff
1295 |+     /** Normalize the URL components
1296 |+ 
1297 |+         Applies Syntax-based normalization to the
```

> Username: vinniefalco  
> Created_at: 2022-03-04 05:03:40 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819266979  

No one knows what "Syntax-based normalization" is. You need to link to the section of the RFC below.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 05:03:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899807006  

📁 include/boost/url/url.hpp

```diff
1305 |+         to unreserved characters are decoded
1306 |+         @li Path-segments in non-relative paths
1307 |+         are normalized with the remove_dot_segments
```

> Username: vinniefalco  
> Created_at: 2022-03-04 05:03:47 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819267007  

no one knows what "remove_dot_segments" is


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 05:13:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899810871  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 113 |+     auto rhs_it = rhs.begin();
 114 |+     char lhs_c = '\0';
 115 |+     char rhs_c = '\0';
```

> Username: vinniefalco  
> Created_at: 2022-03-04 05:13:00 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819270025  

instead of all this lhs rhs nonsense which can hypnotize you with its repetition and slight dissimilarities, how about these names;  
```  
n0, n1, it0, it1, c0, c1  
```


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 05:14:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899811308  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 115 |+     char rhs_c = '\0';
 116 |+     while (lhs_it < lhs.end() &&
 117 |+            rhs_it < rhs.end())
```

> Username: vinniefalco  
> Created_at: 2022-03-04 05:14:03 UTC  
> Updated_at: 2022-03-04 05:14:04 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819270366  

you might want to cache the values of `end()`


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 05:14:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899811393  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 121 |+         if ( lhs_c < rhs_c )
 122 |+             return -1;
 123 |+         else if ( rhs_c < lhs_c )
```

> Username: vinniefalco  
> Created_at: 2022-03-04 05:14:15 UTC  
> Updated_at: 2022-03-04 05:14:16 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819270440  

no need for `else` here


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-04 05:19:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-899813653  

📁 include/boost/url/grammar/ascii.hpp

```diff
  63 |+ {
  64 |+     return (c >= 'a' && c <= 'z')
  65 |+         ? c - ('a' - 'A') : c;
```

> Username: vinniefalco  
> Created_at: 2022-03-04 05:19:14 UTC  
> Updated_at: 2022-03-04 05:19:15 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r819272127  

These have to be on separate lines


---

## Review 11 [Commented]

> Username: alandefreitas  
> Created_at: 2022-03-05 16:28:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-900989918  

📁 include/boost/url/impl/url.ipp

```diff
2130 |+         p_dest, p_end, p, is_path_absolute());
2131 |+     if (n != pn) {
2132 |+         resize_impl(id_path, n);
```

> Username: alandefreitas  
> Created_at: 2022-03-05 16:28:32 UTC  
> Updated_at: 2022-03-05 16:29:56 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820123091  

I think we still need to update some url_view pre-processed data here. I'll compare that to other functions.

> Username: alandefreitas  
> Created_at: 2022-03-07 18:36:34 UTC  
> Updated_at: 2022-03-07 18:36:35 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820993204  

I changed it, but just to remove some redundant conditionals. It's the same logic.   
  
About the pre-processed data, I'm still working on it.


---

## Comment 12

> Username: codecov[bot]  
> Created_at: 2022-03-05 23:21:55 UTC  
> Updated_at: 2022-03-11 18:47:20 UTC  
> Url: https://github.com/boostorg/url/pull/136#issuecomment-1059849649  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/136?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#136](https://codecov.io/gh/CPPAlliance/url/pull/136?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (4dd4e73) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/38cff53f984b0a66b5c1816f8cbdae5e94b2acb3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (38cff53) will **increase** coverage by `0.20%`.  
> The diff coverage is `98.52%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/136/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/136?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #136      +/-   ##  
===========================================  
+ Coverage    96.43%   96.63%   +0.20%       
===========================================  
  Files          108      109       +1       
  Lines         5556     6071     +515       
===========================================  
+ Hits          5358     5867     +509       
- Misses         198      204       +6       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/136?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/impl/pct\_encoding.ipp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGN0X2VuY29kaW5nLmlwcA==) | `62.16% <ø> (ø)` | |  
| [include/boost/url/ipv4\_address.hpp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2NF9hZGRyZXNzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/ipv6\_address.hpp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2Nl9hZGRyZXNzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/rfc/reg\_name\_rule.hpp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL3JlZ19uYW1lX3J1bGUuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlldy5pcHA=) | `99.39% <92.00%> (-0.61%)` | :arrow_down: |  
| [...lude/boost/url/detail/impl/remove\_dot\_segments.ipp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcmVtb3ZlX2RvdF9zZWdtZW50cy5pcHA=) | `98.34% <98.34%> (ø)` | |  
| [include/boost/url/impl/url.ipp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmwuaXBw) | `98.22% <99.18%> (+0.24%)` | :arrow_up: |  
| [include/boost/url/detail/impl/normalize.ipp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvbm9ybWFsaXplLmlwcA==) | `99.30% <99.30%> (ø)` | |  
| [include/boost/url/grammar/ascii.hpp](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9hc2NpaS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [2 more](https://codecov.io/gh/CPPAlliance/url/pull/136/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/136?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/136?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [38cff53...4dd4e73](https://codecov.io/gh/CPPAlliance/url/pull/136?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:13:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902072590  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 167 |+     auto end1 = rhs.end();
 168 |+     char c0 = '\0';
 169 |+     char c1 = '\0';
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:13:09 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820974419  

Oh this looks very nice but hey man... why type `'\0'` when you can type `0`? Prefer typing less, rather than more :) I mean, I would not reject the pull request for this but I'm just trying to save you effort


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:16:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902076115  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 168 |+     char c0 = '\0';
 169 |+     char c1 = '\0';
 170 |+     while (it0 < end0 && it1 < end1)
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:16:23 UTC  
> Updated_at: 2022-03-07 18:16:24 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820976892  

\sigh you need to get in the habit of putting these conditions on separate lines for coverage


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:16:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902076473  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 175 |+             return -1;
 176 |+         if (c1 < c0)
 177 |+             return +1;
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:16:43 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820977111  

+1 is the same as 1 (PTLRTM)


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:18:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902077920  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 185 |+ 
 186 |+ int
 187 |+ icompare(
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:18:05 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820978111  

We will need to include this function in the public API eventually. Don't do this now but just remember for later so we can remove this version.


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:29:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902090980  

📁 include/boost/url/detail/impl/remove_dot_segments.hpp

```diff
  19 |+                   std::char_traits<char>,
  20 |+                   Allocator>
  21 |+ remove_dot_segments(
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:29:47 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820987552  

are we using this?


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:32:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902094459  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
  87 |+             continue;
  88 |+         }
  89 |+         break;
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:32:42 UTC  
> Updated_at: 2022-03-07 18:32:43 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820990002  

You can reorder these statements to remove a line:  
```  
...  
    if( ! s.starts_with("./") )  
        break;  
    s.remove_prefix(2);  
}  
```  
  
There are multiple benefits here. It is easier to read since it loops again where people expect. The conditional is expressed so that the most likely outcome evaluates to true (some optimizers care about this). And it is less code.


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:33:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902095080  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 106 |+             }
 107 |+         }
 108 |+         s.remove_prefix(s.size());
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:33:15 UTC  
> Updated_at: 2022-03-07 18:33:16 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820990444  

isn't this the same as `s = {};`? (PLCRTM)

> Username: alandefreitas  
> Created_at: 2022-03-07 18:40:35 UTC  
> Updated_at: 2022-03-07 18:40:36 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820996344  

Yes. It's almost the same. This was there because I was following a pattern when writing the algorithm and forgot to replace some of these.


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:34:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902096751  

📁 include/boost/url/detail/pct_encoding.hpp

```diff
  49 |+ BOOST_URL_DECL
  50 |+ int
  51 |+ icompare(
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:34:48 UTC  
> Updated_at: 2022-03-07 18:34:49 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820991733  

Do these functions need to be exported? Only if they are called from an inline function. Otherwise they are internal to the shared library.

> Username: alandefreitas  
> Created_at: 2022-03-07 18:41:59 UTC  
> Updated_at: 2022-03-07 18:42:00 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820997405  

I don't know. I was following the pattern from the `key_equal_encoded` function above. Should I remove `BOOST_URL_DECL` from all of them?

> Username: vinniefalco  
> Created_at: 2022-03-07 21:54:28 UTC  
> Updated_at: 2022-03-07 21:55:48 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821135339  

You should find out where they are called and determine whether they should be exported or not. The "pattern" is that functions which are called invoked from functions emitted in the depender's code. This includes the user's functions but it also includes inline functions provided by Boost.URL whose definitions are emitted into the caller's object code.


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:35:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902097036  

📁 include/boost/url/detail/remove_dot_segments.hpp

```diff
  31 |+     std::char_traits<char>,
  32 |+         Allocator>
  33 |+ remove_dot_segments(
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:35:04 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820991958  

Is this used?

> Username: alandefreitas  
> Created_at: 2022-03-07 18:51:20 UTC  
> Updated_at: 2022-03-07 18:51:21 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821004881  

Not really. I used to be used. But it's just being used in tests now. I'll remove it and allocate the string directly in the tests.


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:35:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902097425  

📁 include/boost/url/grammar/ascii.hpp

```diff
  41 | char
  42 | ascii_tolower(char c) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:35:25 UTC  
> Updated_at: 2022-03-07 18:35:26 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820992235  

Can this be constexpr? Should it?

> Username: alandefreitas  
> Created_at: 2022-03-07 18:52:45 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821005988  

I replicated the pattern from `ascii_tolower` but didn't think much about it before. I don't see why they shouldn't be `constexpr`.

> Username: vinniefalco  
> Created_at: 2022-03-10 01:46:57 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823263027  

Oh it was already constexpr, but github diff just didn't show it..


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:43:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902106095  

📁 include/boost/url/impl/url.ipp

```diff
2054 |+                       id_query, id_frag};
2055 |+     int next_id_offset = offset(id_scheme);
2056 |+     for (auto id: all_ids)
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:43:48 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820998785  

I don't think this is right

> Username: alandefreitas  
> Created_at: 2022-03-07 18:53:19 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821006444  

I'll go through that and compare to other functions to check how this can improve.

> Username: vinniefalco  
> Created_at: 2022-03-07 19:17:50 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821025185  

Each of the 5 parts of the URL in the generic syntax uses its own charset for percent-encoding, to determine what is reserved. Scheme for example, has no escapes at all.

> Username: alandefreitas  
> Created_at: 2022-03-07 19:24:52 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821030757  

The RFC says in [Section 6.2.2.2](https://datatracker.ietf.org/doc/html/rfc3986#section-6.2.2.2)  
  
> In addition to the case normalization issue noted above, some URI producers percent-encode octets that do not require percent-encoding, resulting in URIs that are equivalent to their non-encoded counterparts. These URIs should be normalized by decoding any percent-encoded octet that corresponds to an unreserved character, as described in [Section 2.3](https://datatracker.ietf.org/doc/html/rfc3986#section-2.3).  
  
I think there's a difference between necessary and sufficient conditions in these algorithms. Unlike the usual algorithm to produce URLs, normalization is not trying to validate or sanitize what needs to be encoded. Instead, it's trying to identify remove unnecessary encoding and explicitly defines these as the unreserved characters only.  
  
Of course, we can expand that to other characters and decode whatever could be already decoded in the component. I'm fine with this because it would be much better in terms of avoiding false positives. It's just deviating slightly from the RFC though.

> Username: alandefreitas  
> Created_at: 2022-03-07 20:38:30 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821083334  

Good news. I checked javascript's URL and it also decodes chars that are accepted in that component. So it's one more reason to do it.   
  
```js  
new URL('http://%68examPle.com:80/path/../index.html?gliu%5B%5D=1');  
```  
  
```js  
URL {  
  href: 'http://hexample.com/index.html?gliu%5B%5D=1',  
  origin: 'http://hexample.com',  
  protocol: 'http:',  
  username: '',  
  password: '',  
  host: 'hexample.com',  
  hostname: 'hexample.com',  
  port: '',  
  pathname: '/index.html',  
  search: '?gliu%5B%5D=1',  
  searchParams: URLSearchParams { 'gliu[]' => '1' },  
  hash: ''  
}  
```  
  
This URL class is more focused on HTTP URLs though. For instance, port 80 is normalized to `""`.

> Username: vinniefalco  
> Created_at: 2022-03-07 22:35:51 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821161911  

I guess once you break up the algorithm into 5 individual functions (scheme, authority, path, query, and fragment) then changing it will be easier. So we shouldn't worry too much about the character set.


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:44:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902107129  

📁 include/boost/url/impl/url.ipp

```diff
2101 |+     offset_[id_end] = dest - s_;
2102 |+ 
2103 |+     // scheme and host to lowercase
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:44:33 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r820999519  

These need to be in their own functions `normalize_scheme` and `normalize_host`

> Username: alandefreitas  
> Created_at: 2022-03-07 18:53:40 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821006704  

Yep. I'll create `normalize_*` functions then.


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:45:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902108210  

📁 include/boost/url/impl/url_view.ipp

```diff
 467 |+         return comp;
 468 |+ 
 469 |+     comp = detail::remove_dot_segments_compare(
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:45:34 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821000336  

wouldn't this be more appropriately named `compare_encoded_path`

> Username: alandefreitas  
> Created_at: 2022-03-07 18:59:48 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821011522  

There might be a better name for this. Maybe `compare_path_as_normalized` or something. But I don't think `compare_encoded_path` is a more appropriate name because it's comparing _as if_ remove_dot_segments was applied to the string and not only _as if_ encoding was applied, which is what `pct_decode_icompare_unchecked` does.

> Username: vinniefalco  
> Created_at: 2022-03-07 19:18:49 UTC  
> Updated_at: 2022-03-07 19:18:50 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821025886  

it is an implementation detail though, so the requirements for names can be relaxed a little bit. If it was public, that'd be another story.


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:47:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902110380  

📁 include/boost/url/rfc/reg_name_rule.hpp

```diff
  37 |     pct_encoded_str v;
  38 | 
  39 |-     BOOST_URL_DECL
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:47:34 UTC  
> Updated_at: 2022-03-07 18:47:35 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821002017  

it would have been better if these unrelated changes were in their own commit. too late now but something to think about in the future.

> Username: alandefreitas  
> Created_at: 2022-03-07 19:00:30 UTC  
> Updated_at: 2022-03-07 19:00:56 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821012093  

Yep. That was causing a problem with "GCC" on windows. I think we missed the problem because of a problem with CI on that occasion.


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:48:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902111162  

📁 include/boost/url/url.hpp

```diff
1305 |+         @li Percent-encoded octets that correspond
1306 |+         to unreserved characters are decoded
1307 |+         @li Redundant path-segments are removed
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:48:18 UTC  
> Updated_at: 2022-03-07 18:48:37 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821002555  

See, this documentation would be much nicer and clearer if it just said that it calls the functions `normalize_scheme`, `normalize_authority`, etc..  and then each of those functions could have more detailed docs.  
  
This is called the "Ramey Rule" - see me for explanation.

> Username: alandefreitas  
> Created_at: 2022-03-07 19:01:28 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821012836  

Yes. I'll make `normalize_scheme`, ... exist and then update that.


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:49:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902112632  

📁 include/boost/url/url.hpp

```diff
1317 |     */
1318 |     BOOST_URL_DECL
1319 |     url&
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:49:40 UTC  
> Updated_at: 2022-03-07 18:49:41 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821003577  

I don't think `resolve` should be marked inline?

> Username: alandefreitas  
> Created_at: 2022-03-07 19:08:51 UTC  
> Updated_at: 2022-03-07 19:08:52 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821018751  

Do you mean `resolve` or `normalize`? `normalize` is not `inline`, and this PR didn't touch the `resolve` function. Do you want me to remove `inline` from `resolve` and move it to the `.ipp` file in this PR?

> Username: vinniefalco  
> Created_at: 2022-03-07 19:20:01 UTC  
> Updated_at: 2022-03-07 19:20:02 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821026901  

`resolve`. But its already in the .ipp file.

> Username: alandefreitas  
> Created_at: 2022-03-07 19:35:35 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821038901  

The member function `resolve` is not `inline` and is defined in the `.ipp` file.  
  
https://github.com/alandefreitas/url/blob/11c76e7395821e254d4bd0fa0edc8ccd0ea38a43/include/boost/url/url.hpp#L1366-L1372  
  
https://github.com/alandefreitas/url/blob/11c76e7395821e254d4bd0fa0edc8ccd0ea38a43/include/boost/url/impl/url.ipp#L1827-L1833  
  
The `friend` function `resolve` is `inline` and defined in the `.hpp` file:  
  
https://github.com/alandefreitas/url/blob/11c76e7395821e254d4bd0fa0edc8ccd0ea38a43/include/boost/url/url.hpp#L1328-L1335  
  
https://github.com/alandefreitas/url/blob/11c76e7395821e254d4bd0fa0edc8ccd0ea38a43/include/boost/url/url.hpp#L1415-L1426  
  
So do you want me to change the `friend` function?

> Username: vinniefalco  
> Created_at: 2022-03-07 22:02:23 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821140848  

ugh no.. I guess its right.


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:52:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902115892  

📁 include/boost/url/url_view.hpp

```diff
1710 |+     /** Compare two URLs.
1711 |+ 
1712 |+         This function compares two algorithms
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:52:41 UTC  
> Updated_at: 2022-03-07 18:52:42 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821005938  

you are saying algorithm twice, and the usage of the first occurence is wrong. We are comparing two URLs not two algorithms...


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:53:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902116305  

📁 test/unit/rfc/paths_rule.cpp

```diff
 208 |+                 BOOST_TEST(e2 == s2);
 209 |+ 
 210 |+                 BOOST_TEST(detail::remove_dot_segments_compare(p, e1, true) == 0);
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:53:05 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821006242  

well you can just put this test function into the .cpp file


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:53:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902116631  

📁 test/unit/rfc/paths_rule.cpp

```diff
 201 |+                    string_view e1,
 202 |+                    string_view e2) {
 203 |+                 std::allocator<char> a{};
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:53:22 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821006484  

unused?


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:53:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902117060  

📁 test/unit/rfc/paths_rule.cpp

```diff
 194 | 
 195 |+     void
 196 |+     test_normalize()
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:53:47 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821006793  

This should be broken up into individual functions like `testScheme`, `testAuthority`, etc. And then maybe one `testAll` at the end.

> Username: alandefreitas  
> Created_at: 2022-03-07 19:13:14 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821021841  

There's no `remove_dot_segments` for scheme, authority, etc... Only paths. This function could be called `test_remove_dot_segments` but I was trying to type less.  
  
This is just testing `remove_dot_segments`, which is an auxiliary algorithm that only applies to normalizing paths. The tests considering all components are in `url_view.cpp`.

> Username: vinniefalco  
> Created_at: 2022-03-07 19:20:38 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821027502  

oh... well, `testPath` then.

> Username: alandefreitas  
> Created_at: 2022-03-07 19:37:28 UTC  
> Updated_at: 2022-03-07 19:37:29 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821040305  

This is not very descriptive because all tests in this file are testing paths too  
  
https://github.com/alandefreitas/url/blob/11c76e7395821e254d4bd0fa0edc8ccd0ea38a43/test/unit/rfc/paths_rule.cpp#L252-L256

> Username: vinniefalco  
> Created_at: 2022-03-07 22:36:40 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821162400  

Thats a different file though. there's no need to pick globally unique names of test functions...


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 18:54:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902117403  

📁 test/unit/rfc/paths_rule.cpp

```diff
 233 |+                 "./a/b/c/./../../g", // "a/g"
 234 |+                 "/a/b/../../../g",   // "/../g"
 235 |+                 false) == +1);
```

> Username: vinniefalco  
> Created_at: 2022-03-07 18:54:06 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821007036  

Isn't `+1` the same as `1` ?

> Username: alandefreitas  
> Created_at: 2022-03-07 19:15:01 UTC  
> Updated_at: 2022-03-07 19:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821023153  

Yep. I just noticed people, including the pros, tend to use +1 in `compare` or `operator<=>` algorithms to make it more explicit:  
  
https://github.com/boostorg/core/blob/dbefea96316465c501bcb853642cc855b03a462b/include/boost/core/detail/string_view.hpp#L589  
  
I was trying to replicate that but I'll remove it to make it more consistent with the rest of Boost.URL.

> Username: vinniefalco  
> Created_at: 2022-03-07 22:04:40 UTC  
> Updated_at: 2022-03-07 22:04:41 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821142381  

The problem with "+1" is that it is weird, and makes me waste time wondering if the code would work the same way if it was written as just "1". There is a time for "+1" which is in a lambda without a specified return type that wants to return an unsigned (or something like that). But this is not that so it should not get fancy.  
  
I asked Peter why he used +1 in string_view and he said "just for looks." In other words there is no difference between +1 and 1 in this context.

> Username: alandefreitas  
> Created_at: 2022-03-08 00:53:50 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821230789  

Yep. I knew all along it was just for looks. It looked better to me too. Anyway, I've changed it.


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 19:21:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902147728  

📁 test/unit/rfc/paths_rule.cpp

```diff
 202 |+                    string_view e2) {
 203 |+                 std::allocator<char> a{};
 204 |+                 auto s1 = detail::remove_dot_segments(p, a, true);
```

> Username: vinniefalco  
> Created_at: 2022-03-07 19:21:22 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821028049  

You should avoid invoking non-public functions in tests, when possible. In other words it is better to test using what users will actually be calling.

> Username: alandefreitas  
> Created_at: 2022-03-07 19:40:28 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821042478  

I considered that and it's what I did for other tests. The problem is this algorithm is quite complex and there are lots of sensitive code paths and conditions to test. We could move that to `url_view.cpp` and find some URLs that trigger the same code paths, but the tests are going to become a little bulkier.

> Username: vinniefalco  
> Created_at: 2022-03-07 22:37:50 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821163117  

You can't move it to url_view.cpp you have to put it in url.cpp, because it has to modify the segments no? But yes, it is better to get full coverage on `remove_dot_segments` using public APIs instead of invoking the implementation function directly. It will require more tests but it is worth it.


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 21:53:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902297341  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 430 |+     while (lhs_i > 0 && rhs_i > 0)
 431 |+     {
 432 |+         // Consume more children
```

> Username: vinniefalco  
> Created_at: 2022-03-07 21:53:48 UTC  
> Updated_at: 2022-03-07 21:53:49 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821134892  

the optics on this comment are terrible


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 22:01:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902304404  

📁 include/boost/url/url.hpp

```diff
1353 |+     BOOST_URL_DECL
1354 |+     url&
1355 |+     normalize_user();
```

> Username: vinniefalco  
> Created_at: 2022-03-07 22:01:08 UTC  
> Updated_at: 2022-03-07 22:01:09 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821139989  

there's no "user" there is just _authority_


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 22:01:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902304605  

📁 include/boost/url/url.hpp

```diff
1376 |+     BOOST_URL_DECL
1377 |+     url&
1378 |+     normalize_password();
```

> Username: vinniefalco  
> Created_at: 2022-03-07 22:01:21 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821140138  

there's no password there is just _authority_


---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 22:01:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902304920  

📁 include/boost/url/url.hpp

```diff
1423 |+     BOOST_URL_DECL
1424 |+     url&
1425 |+     normalize_port();
```

> Username: vinniefalco  
> Created_at: 2022-03-07 22:01:42 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821140375  

There's no port there is just _authority_


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-07 22:01:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902305102  

📁 include/boost/url/url.hpp

```diff
1400 |+     BOOST_URL_DECL
1401 |+     url&
1402 |+     normalize_host();
```

> Username: vinniefalco  
> Created_at: 2022-03-07 22:01:53 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821140501  

there's no host there is just _authority_


---

## Review 40 [Commented]

> Username: alandefreitas  
> Created_at: 2022-03-08 03:48:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-902513115  

📁 include/boost/url/impl/url.ipp

```diff
2090 |+ 
2091 |+     // normalize password
2092 |+     static constexpr auto pass_cs =
```

> Username: alandefreitas  
> Created_at: 2022-03-08 03:48:48 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821297690  

These charsets are coming from other `set_encoded_*` functions or from the corresponding grammar rule. It's probably appropriate to define these in a single place but I don't know where the best place for that would be.   
  
I also noticed Javascript's URL class 1) normalizes all URLs, and 2) is more permissive for `reg_name_chars` (it accepts subdelim_chars). For instance, the host would decode a `!`:  
  
```js  
new URL('http://%68examPle%21.com:80/path/../index.html?gliu%5B%5D=1');  
```  
  
```js  
URL {  
  href: 'http://hexample!.com/index.html?gliu%5B%5D=1',  
  origin: 'http://hexample!.com',  
  protocol: 'http:',  
  username: '',  
  password: '',  
  host: 'hexample!.com',  
  hostname: 'hexample!.com',  
  port: '',  
  pathname: '/index.html',  
  search: '?gliu%5B%5D=1',  
  searchParams: URLSearchParams { 'gliu[]' => '1' },  
  hash: ''  
}  
```

> Username: vinniefalco  
> Created_at: 2022-03-08 05:13:48 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r821327550  

if you want to share the charsets put it in `<boost/url/detail/charsets.hpp>` and make sure it is not visible publicly (i.e. only included from .ipp files)


---

## Comment 41

> Username: alandefreitas  
> Created_at: 2022-03-09 00:00:15 UTC  
> Url: https://github.com/boostorg/url/pull/136#issuecomment-1062408247  

So I improved coverage and worked on the tricky edges cases. It seems like all tests are fine.

---

## Review 42 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-09 00:41:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-903747583  

📁 include/boost/url/detail/charsets.hpp

```diff
   9 |+ 
  10 |+ #ifndef BOOST_URL_DETAIL_CHARSETS_HPP
  11 |+ #define BOOST_URL_DETAIL_CHARSETS_HPP
```

> Username: vinniefalco  
> Created_at: 2022-03-09 00:41:27 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r822194995  

sorry... I meant that this file should go in `<boost/url/rfc/detail/charsets.hpp>`


---

## Review 43 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-09 00:45:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-903749699  

📁 include/boost/url/impl/url.hpp

```diff
  30 |+ normalize_octets_impl(
  31 |+     int id,
  32 |+     Charset const& cs) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-03-09 00:45:36 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r822196658  

if this function is only ever used internally, consider changing `Charset const&` to `grammar::lut_chars const&`. This way, if the function is ever called with something that is not a `lut_chars`, we will be alerted with a compiler error. Otherwise, we would silently emit two copies of this function in the binary - and a goal of the library is to minimize the amount of object code.


---

## Comment 44

> Username: alandefreitas  
> Created_at: 2022-03-09 00:55:28 UTC  
> Url: https://github.com/boostorg/url/pull/136#issuecomment-1062441987  

Amended :)

---

## Review 45 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:34:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905206119  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 273 |+             string_view& c,
 274 |+             std::size_t& l,
 275 |+             bool r)
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:34:38 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823258028  

weird formatting


---

## Review 46 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:35:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905206518  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
  84 | 
  85 |+ int
  86 |+ pct_decode_compare_unchecked(
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:35:29 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823258363  

these really don't belong here in pct_encoding.ipp, anything percent-encoding related that is strictly for comparison, normalization, or hash functions should have its own header, for example `detail/normalize.hpp`. Or something.


---

## Review 47 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:36:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905206965  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
  42 |+         char* const first = dest0;
  43 |+         char* last = dest;
  44 |+         while (last != first) {
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:36:19 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823258701  

weird format


---

## Review 48 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:38:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905207931  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 360 |+             return;
 361 |+         }
 362 |+         else if (l)
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:38:15 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823259461  

`if( 1 )` ?


---

## Review 49 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:39:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905208337  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 388 |+             pop_last(p, c, s, r);
 389 |+             n += pct_decode_bytes_unchecked(c);
 390 |+         } while (!c.empty());
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:39:05 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823259783  

weird format

> Username: alandefreitas  
> Created_at: 2022-03-10 21:03:10 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824154317  

Why?

> Username: vinniefalco  
> Created_at: 2022-03-11 01:39:40 UTC  
> Updated_at: 2022-03-11 01:39:41 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824318984  

the brace is not on its own line, so line 374 mixes destructors with the loop condition


---

## Review 50 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:40:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905208858  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 488 |+         // Compare intersection
 489 |+         while (!s0c.empty() &&
 490 |+                !s1c.empty())
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:40:04 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823260166  

weird format

> Username: alandefreitas  
> Created_at: 2022-03-10 21:03:14 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824154353  

Why?

> Username: vinniefalco  
> Created_at: 2022-03-11 01:41:17 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824319575  

the attempt at vertical alignment  
```  
        while(  
            !s0c.empty() &&  
            !s1c.empty());  
```  
  
I usually never vertically align, because it puts things off the tab stops (e.g. line 467 is indented 15 spaces)


---

## Review 51 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:40:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905209012  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 496 |+                 cmp = -1;
 497 |+             else if (rhs_c < lhs_c)
 498 |+                 cmp = +1;
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:40:23 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823260300  

here we go with +1 again. I like that these don't use braces I wish the rest of it was like that


---

## Review 52 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:40:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905209214  

📁 include/boost/url/detail/pct_encoding.hpp

```diff
  39 |+ pct_decode_compare_unchecked(
  40 |+     string_view lhs,
  41 |+     string_view rhs) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:40:47 UTC  
> Updated_at: 2022-03-10 01:40:48 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823260500  

Are these called from user code?

> Username: alandefreitas  
> Created_at: 2022-03-10 21:04:06 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824154905  

No. Removing `BOOST_URL_DECL`, right? I replicated that from `key_equal_encoded` at first but now removed it.

> Username: vinniefalco  
> Created_at: 2022-03-11 01:42:03 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824319908  

`key_equal_encoded` might be needed for users at some point which is why I might have exported it, but I probably decided to just wait until someone needed it.


---

## Review 53 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:41:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905209596  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 180 |+ 
 181 |+ std::size_t
 182 |+ pct_decode_ends_with_unchecked(
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:41:33 UTC  
> Updated_at: 2022-03-10 01:41:34 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823260819  

These functions are complicated enough that they deserve at least a sentence explaining what it does, as I cannot figure it out from the name alone, or the context.


---

## Review 54 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:41:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905209667  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 186 |+     auto consume_last = []
 187 |+         (string_view::iterator& it,
 188 |+          string_view::iterator& end,
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:41:42 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823260875  

weird format

> Username: alandefreitas  
> Created_at: 2022-03-10 21:04:15 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824155006  

Why?


---

## Review 55 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:42:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905209976  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 210 |+     char c1 = 0;
 211 |+     while (it0 < end0 &&
 212 |+            it1 < end1)
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:42:15 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823261096  

weird format

> Username: alandefreitas  
> Created_at: 2022-03-10 21:04:21 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824155075  

Why?


---

## Review 56 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:42:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905210092  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
 228 |+ {
 229 |+     auto consume_one = []
 230 |+         (string_view::iterator& it,
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:42:26 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823261191  

weird format

> Username: alandefreitas  
> Created_at: 2022-03-10 21:04:25 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824155142  

Why?


---

## Review 57 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:42:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905210405  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 270 |+ 
 271 |+     auto pop_last = [](
 272 |+             string_view& s,
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:42:58 UTC  
> Updated_at: 2022-03-10 01:42:59 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823261435  

weird format

> Username: alandefreitas  
> Created_at: 2022-03-10 21:04:29 UTC  
> Updated_at: 2022-03-10 21:04:30 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824155186  

Why?


---

## Review 58 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:43:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905210826  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 315 |+                 continue;
 316 |+             }
 317 |+             n = detail::pct_decode_ends_with_unchecked(s, "/..");
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:43:52 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823261783  

ah is this like `string_view::ends_with` but with applying percent-decoding?

> Username: alandefreitas  
> Created_at: 2022-03-10 21:04:39 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824155273  

Exactly.


---

## Review 59 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:44:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905211297  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 379 |+     auto norm_bytes = [&pop_last](
 380 |+          string_view p,
 381 |+          bool r)
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:44:50 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823262168  

Do you have tabs set to 5 spaces or something?

> Username: alandefreitas  
> Created_at: 2022-03-10 21:05:21 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824155891  

I'm not using automatic formatting with boost.url. I'm mostly trying to mimic the unspoken rules.


---

## Review 60 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:45:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905211645  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 195 |+     string_view s1_init,
 196 |+     bool s0_remove_unmatched,
 197 |+     bool s1_remove_unmatched) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:45:31 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823262455  

What do these bools do?

> Username: alandefreitas  
> Created_at: 2022-03-10 21:06:34 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824157148  

They remove unmatched `..`s, like RFC requires for absolute paths or keep the unmatched `..`, like we expect for relative references.


---

## Review 61 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:46:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905212084  

📁 include/boost/url/detail/remove_dot_segments.hpp

```diff
  31 |+     string_view s0_init,
  32 |+     string_view s1_init,
  33 |+     bool l,
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:46:25 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823262813  

oh this looks like the number one...


---

## Review 62 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:47:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905212522  

📁 include/boost/url/grammar/ascii.hpp

```diff
  52 |+ 
  53 |+     This function returns the uppercase equivalent
  54 |+     if a character is an upper-case letter, otherwise
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:47:16 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823263171  

should say "lower-case"


---

## Review 63 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:47:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905212759  

📁 include/boost/url/impl/url.hpp

```diff
  11 | #define BOOST_URL_IMPL_URL_HPP
  12 | 
  13 |+ #include <boost/url/grammar/ascii.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:47:40 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823263360  

What's this for?


---

## Review 64 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:51:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905214531  

📁 include/boost/url/impl/url.ipp

```diff
 751 |     auto const n =
 767 |-         pct_encode_bytes(s, {}, cs);
 752 |+         pct_encode_bytes(s, {}, detail::host_chars);
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:51:06 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823264739  

killing my feng-shui here  
```  
    auto const n = pct_encode_bytes(  
        s, {}, detail::host_chars);  
```


---

## Review 65 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:51:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905214836  

📁 include/boost/url/impl/url.ipp

```diff
1788 |     check_invariants();
1804 |-     static constexpr auto cs =
1805 |-         pchars + '/' + '?';
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:51:46 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823264961  

Good, you got rid of this function-local static and used the variable that was already there?

> Username: alandefreitas  
> Created_at: 2022-03-10 21:08:36 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824158828  

Yes. Everything is in `boost/url/rfc/detail/charsets.hpp` now. This also avoids accidents, because these charsets were being replicated at multiple places.


---

## Review 66 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:54:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905216200  

📁 include/boost/url/impl/url.ipp

```diff
2088 |+         while(it < end)
2089 |+         {
2090 |+             if (*it != '%')
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:54:37 UTC  
> Updated_at: 2022-03-10 01:54:46 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823266034  

but.. scheme will never have a percent


---

## Review 67 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:55:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905216631  

📁 include/boost/url/impl/url.ipp

```diff
2126 |+     // normalize user
2127 |+     normalize_octets_impl(id_user, detail::user_chars);
2128 |+     return *this;
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:55:33 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823266377  

ooh this function is looking good


---

## Review 68 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:56:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905217191  

📁 include/boost/url/impl/url.ipp

```diff
2122 |+ 
2123 |+     // normalize password
2124 |+     normalize_octets_impl(id_pass, detail::password_chars);
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:56:42 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823266828  

password holds the leading colon, right? we are giving that character to `normalize_octets_impl`

> Username: alandefreitas  
> Created_at: 2022-03-10 21:09:23 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824159425  

> password holds the leading colon, right?  
  
I think so.   
  
> we are giving that character to normalize_octets_impl  
  
That's fine. `normalize_octets_impl` doesn't encode stuff.


---

## Review 69 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:57:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905217427  

📁 include/boost/url/impl/url.ipp

```diff
2141 |+     std::size_t n = detail::remove_dot_segments(
2142 |+         p_dest, p_end, p, abs);
2143 |+     if (n != pn) {
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:57:07 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823267038  

weird format


---

## Review 70 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:57:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905217499  

📁 include/boost/url/impl/url.ipp

```diff
2147 |+                     p.begin() + 1,
2148 |+                     p.end(),
2149 |+                     '/') + 1;
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:57:16 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823267094  

weird format


---

## Review 71 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:57:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905217664  

📁 include/boost/url/impl/url.ipp

```diff
2176 |+     normalize_path();
2177 |+     normalize_authority();
2178 |+     normalize_scheme();
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:57:37 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823267244  

NICE


---

## Review 72 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 01:58:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905218323  

📁 include/boost/url/impl/url.ipp

```diff
2073 |+         std::size_t diff = it - dest;
2074 |+         std::size_t s = len(id) - diff;
2075 |+         shrink_impl(id, s);
```

> Username: vinniefalco  
> Created_at: 2022-03-10 01:58:58 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823267787  

Why aren't we just calling `resize(id,s)`?

> Username: vinniefalco  
> Created_at: 2022-03-10 01:59:42 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823268070  

Hmm `s` as an integer is confusing can we please use `n` for sizes and `s` for strings or string views? I started to look to `shrink_impl` to understand why it was being passed a string...

> Username: alandefreitas  
> Created_at: 2022-03-10 21:13:42 UTC  
> Updated_at: 2022-03-10 21:13:43 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824162204  

> Why aren't we just calling resize(id,s)?  
  
Resize also calls `shrink_impl`.  `shrink_impl` is just the snippet from `resize_impl` when the new size is smaller.  
  
The difference is only `resize_impl` would (i) redundantly check if we need to increase or decrease the size, (ii) make `noexcept` impossible if we ever want to, and (iii) semantics, to express directly in code that normalization never allocates.


---

## Review 73 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 02:00:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905219297  

📁 test/unit/url.cpp

```diff
1940 |         testResolution();
1941 |         testOstream();
1942 |+         testNormalize();
```

> Username: vinniefalco  
> Created_at: 2022-03-10 02:00:53 UTC  
> Updated_at: 2022-03-10 02:00:54 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823268583  

Tests look nice and light, how is coverage?

> Username: alandefreitas  
> Created_at: 2022-03-10 21:17:36 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824165002  

Almost 100% covered.


---

## Review 74 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-10 02:03:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-905220530  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 157 |+                 append(s.substr(0, 1));
 158 |+             }
 159 |+             else if (!remove_unmatched)
```

> Username: vinniefalco  
> Created_at: 2022-03-10 02:03:30 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r823269619  

You are testing `remove_unmatched` and then testing `! remove_unmatched`... and these lines are not covered.


---

## Comment 75

> Username: vinniefalco  
> Created_at: 2022-03-10 02:04:25 UTC  
> Url: https://github.com/boostorg/url/pull/136#issuecomment-1063577573  

A few uncovered lines:  
https://app.codecov.io/gh/CPPAlliance/url/blob/ee3e5b58387413b8758c5bdd73b4a8efafd1171e/include/boost/url/detail/impl/remove_dot_segments.ipp  
  
And in pct_encoding.ipp

---

## Comment 76

> Username: vinniefalco  
> Created_at: 2022-03-10 02:04:34 UTC  
> Url: https://github.com/boostorg/url/pull/136#issuecomment-1063577650  

This is almost ready !!!!

---

## Comment 77

> Username: alandefreitas  
> Created_at: 2022-03-10 21:21:43 UTC  
> Url: https://github.com/boostorg/url/pull/136#issuecomment-1064515994  

> A few uncovered lines:  
> https://app.codecov.io/gh/CPPAlliance/url/blob/ee3e5b58387413b8758c5bdd73b4a8efafd1171e/include/boost/url/detail/impl/remove_dot_segments.ipp  
> And in pct_encoding.ipp  
  
Oh. I think I accidentally uncovered these lines by changing another test.  
  
I believe all lines that can be covered are covered now. Only two lines in `remove_dot_segments.hpp` are impossible to reach indirectly because they would require `remove_unmatched` on a relative path or vice-versa. The implementation should have these lines to be correct but `url_view` can never call the function in a way that hits these lines.

---

## Comment 78

> Username: alandefreitas  
> Created_at: 2022-03-11 04:28:06 UTC  
> Url: https://github.com/boostorg/url/pull/136#issuecomment-1064761026  

Including update to [differentiate encoded slashes](https://github.com/CPPAlliance/url/pull/136/commits/a34241d615364f337e3e9cbf644482574114ff6d)

---

## Review 79 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-11 15:01:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-907339911  

📁 include/boost/url/detail/impl/normalize.ipp

```diff
 232 |+         }
 233 |+         c = *std::prev(end);
 234 |+         --end;
```

> Username: vinniefalco  
> Created_at: 2022-03-11 15:01:28 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824797553  

can you replace that with  
```  
c = *--end;  
```  
or  
```  
--end;  
c = *end;  
```  
?


---

## Review 80 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-11 15:02:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-907341647  

📁 include/boost/url/detail/impl/normalize.ipp

```diff
 218 |+         {
 219 |+             c = *std::prev(end);
 220 |+             --end;
```

> Username: vinniefalco  
> Created_at: 2022-03-11 15:02:45 UTC  
> Updated_at: 2022-03-11 15:02:46 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824799093  

`c = *--end;`?


---

## Review 81 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-11 15:03:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-907342808  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 191 |-     bool s1_remove_unmatched) noexcept
 190 |+     bool r0,
 191 |+     bool r1) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-03-11 15:03:40 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824800303  

it would still be nice to know what these mean

> Username: alandefreitas  
> Created_at: 2022-03-11 15:36:53 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824831495  

The declaration uses `remove_unmatched_lhs` and the definition uses `r0`. I've been changing this back and forth. Any other name recommendation? Or any recommendation of a place to include a comment?


---

## Review 82 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-11 15:08:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-907349103  

📁 include/boost/url/detail/remove_dot_segments.hpp

```diff
  32 |-     bool remove_unmatched_dotdot_rhs) noexcept;
  31 |+     bool remove_unmatched_lhs,
  32 |+     bool remove_unmatched_rhs) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-03-11 15:08:31 UTC  
> Updated_at: 2022-03-11 15:08:48 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824805900  

I still have no idea what these variables mean. Also, while the documentation for a public function goes in the declaration, the documentation for a private function goes in the definition. The reason, is that the user is only supposed to look at public declarations, while maintainers like us will only look at the definition (since that's where all the trouble is!)  
  
This is why once in a while you will see me leave out the formal parameter names in private function declarations, especially if they are obvious types given the context.  
  
I need to know what these bools mean. How can a function that accepts constant views to a string buffer remove anything?

> Username: alandefreitas  
> Created_at: 2022-03-11 15:41:40 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824835966  

I have no problem with any of that. I can rename anything or reinclude the comment explaining what this does. I've been doing this back and forth, I'm just not sure how to do it respecting the feng shui.  
  
> I need to know what these bools mean.   
  
There's a discussion about this somewhere else. RFC determines we remove_dot_segments should remove unmatched ".." segments. But we need to turn this off for relative paths.


---

## Review 83 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-11 15:14:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/136#pullrequestreview-907357016  

📁 test/unit/url.cpp

```diff
1922 |             check("/aa/g", "/aa/gg", -1);
1923 |+ 
1924 |+             check("../a/b", "..%2Fa/b", 1);
```

> Username: vinniefalco  
> Created_at: 2022-03-11 15:14:32 UTC  
> Url: https://github.com/boostorg/url/pull/136#discussion_r824811406  

Do you have a test that includes a percent-encoded dot?


---

## Comment 84

> Username: alandefreitas  
> Created_at: 2022-03-11 15:44:43 UTC  
> Url: https://github.com/boostorg/url/pull/136#issuecomment-1065233595  

Tests running...

---
