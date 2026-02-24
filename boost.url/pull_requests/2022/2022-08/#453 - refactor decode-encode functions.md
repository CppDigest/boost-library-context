# #453 refactor decode/encode functions [Closed]

> Username: alandefreitas  
> Created at: 2022-08-25 00:12:42 UTC  
> Updated at: 2022-08-30 21:01:12 UTC  
> Closed at: 2022-08-25 20:41:12 UTC  
> Url: https://github.com/boostorg/url/pull/453  

fix #448, fix #438

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-08-25 00:20:24 UTC  
> Url: https://github.com/boostorg/url/pull/453#issuecomment-1226625773  

An automated preview of the documentation is available at [https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-25 00:35:24 UTC  
> Url: https://github.com/boostorg/url/pull/453#issuecomment-1226635057  

An automated preview of the documentation is available at [https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-08-25 01:15:22 UTC  
> Url: https://github.com/boostorg/url/pull/453#issuecomment-1226660448  

An automated preview of the documentation is available at [https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-08-25 01:31:14 UTC  
> Updated_at: 2022-08-25 19:45:31 UTC  
> Url: https://github.com/boostorg/url/pull/453#issuecomment-1226668285  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/453?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#453](https://codecov.io/gh/CPPAlliance/url/pull/453?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (7fea29d) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/954eb5b7e6af8228fa3b8a644c6a2e51b29980e7?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (954eb5b) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/453/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/453?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #453   +/-   ##  
========================================  
  Coverage    97.92%   97.92%             
========================================  
  Files          132      132             
  Lines         6349     6349             
========================================  
  Hits          6217     6217             
  Misses         132      132             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/453?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/any\_path\_iter.hpp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9wYXRoX2l0ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/any\_query\_iter.hpp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9xdWVyeV9pdGVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [...nclude/boost/url/detail/segments\_iterator\_impl.hpp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3NlZ21lbnRzX2l0ZXJhdG9yX2ltcGwuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/url\_impl.hpp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3VybF9pbXBsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/not\_empty\_rule.hpp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9ub3RfZW1wdHlfcnVsZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/tuple\_rule.hpp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci90dXBsZV9ydWxlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_encoded\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZF92aWV3LmlwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfdmlldy5pcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/query\_param.ipp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9xdWVyeV9wYXJhbS5pcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments.hpp](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50cy5ocHA=) | `100.00% <ø> (ø)` | |  
| ... and [34 more](https://codecov.io/gh/CPPAlliance/url/pull/453/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/453?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/453?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [954eb5b...7fea29d](https://codecov.io/gh/CPPAlliance/url/pull/453?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 04:17:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1084644244  

📁 include/boost/url/detail/impl/any_query_iter.ipp

```diff
  73 |     string_view s(p_, n_);
  74 |-     auto rn = urls::validate_pct_encoding(
  74 |+     auto rn = urls::detail::validate_encoding(
```

> Username: vinniefalco  
> Created_at: 2022-08-25 00:26:43 UTC  
> Updated_at: 2022-08-25 04:17:55 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954400166  

but here shouldn't we be using `pct_decode` and looking at the `result`?

---

📁 include/boost/url/detail/impl/any_query_iter.ipp

```diff
 155 |     string_view s(p_, n_);
 156 |-     n += urls::pct_encode_bytes(
 156 |+     n += urls::encode_bytes(
```

> Username: vinniefalco  
> Created_at: 2022-08-25 00:27:05 UTC  
> Updated_at: 2022-08-25 04:17:55 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954400318  

why the namespace qualifier?


📁 include/boost/url/detail/impl/any_path_iter.ipp

```diff
 163 |     string_view s(p_, n_);
 163 |-     n += urls::pct_encode_bytes(
 164 |+     n += urls::encode_bytes(
```

> Username: vinniefalco  
> Created_at: 2022-08-25 00:28:37 UTC  
> Updated_at: 2022-08-25 04:17:55 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954400854  

`encoded_size` sounds much better here

---

📁 include/boost/url/detail/impl/any_path_iter.ipp

```diff
  78 |     string_view s(p_, n_);
  78 |-     auto rn = urls::validate_pct_encoding(
  79 |+     auto rn = urls::detail::validate_encoding(
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:17:47 UTC  
> Updated_at: 2022-08-25 04:17:55 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954485781  

But this is replaced with `pct_decode` now


📁 doc/qbk/0.main.qbk

```diff
  77 |- [def __pct_encoded_rule__       [link url.ref.boost__urls__pct_encoded_rule `pct_encoded_rule`]]
  76 |+ [def __decode_view__            [link url.ref.boost__urls__encoded_view `decode_view`]]
  77 |+ [def __encoded_rule__           [link url.ref.boost__urls__encoded_rule `encoded_rule`]]
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:12:47 UTC  
> Updated_at: 2022-08-25 04:17:55 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954483885  

I wonder if we should leave the rule alone, what do you think? It isn't going to be used much, so the verbosity isnt' so bad. And the name "pct-encoded" comes from the RFC (its the name of the grammar).


📁 doc/qbk/quickref.xml

```diff
  99 |+           <member><link linkend="url.ref.boost__urls__decode_opts">decode_opts</link></member>
 100 |+           <member><link linkend="url.ref.boost__urls__encode_opts">encode_opts</link></member>
 101 |+           <member><link linkend="url.ref.boost__urls__decode_view">decode_view</link></member>
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:13:30 UTC  
> Updated_at: 2022-08-25 04:17:55 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954484173  

These are out of alphabetical order now


📁 include/boost/url/decode_opts.hpp

```diff
  20 |+         @ref decode
  21 |+ */
  22 |+ struct decode_opts
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:16:03 UTC  
> Updated_at: 2022-08-25 04:17:55 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954485114  

Why did we split pct_encoding.hpp up into 4 files? I'm not saying not to do it, but I'm wondering what the rationale is.

> Username: alandefreitas  
> Created_at: 2022-08-25 17:06:13 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955223218  

decode_opts had to go into its own file, then the rest followed from there.

> Username: alandefreitas  
> Created_at: 2022-08-25 17:06:53 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955223727  

That's also convenient because the intersection of dependencies for decoding and encoding is very small.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 04:18:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1084762590  

📁 include/boost/url/impl/encode.hpp

```diff
  73 | std::size_t
 189 |- pct_encode_bytes(
  74 |+ encode_bytes(
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:18:49 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954486172  

`encoded_size`


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 04:20:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1084763490  

📁 include/boost/url/impl/params_encoded_view.ipp

```diff
 121 | 
 122 |- result<params_encoded_view>
 122 |+ result<params_view>
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:20:34 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954486821  

What happened here? We're returning a decoded view instead of encoded? This is an API change

> Username: alandefreitas  
> Created_at: 2022-08-25 17:09:40 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955226063  

Yes. We talked about this before. This would be the only rule to return the encoded result. At the time, you said we could change it later but we had higher priorities. This seemed like a good time to do that because I was already refactoring something that related to this.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 04:21:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1084763885  

📁 include/boost/url/impl/url_base.ipp

```diff
 406 |     auto const n =
 407 |-         detail::pct_encode_bytes_impl(
 407 |+         detail::encode_bytes_impl(
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:21:20 UTC  
> Updated_at: 2022-08-25 04:21:21 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954487085  

`encoded_size_impl` but why do we need our own private function why can't we call `encoded_size`?

> Username: alandefreitas  
> Created_at: 2022-08-25 17:13:14 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955229025  

`s` is not a `string_view`. We could have other overloads taking iterators but we haven't discussed that yet.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 04:21:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1084764148  

📁 include/boost/url/params.hpp

```diff
  15 | #include <boost/url/query_param.hpp>
  16 |- #include <boost/url/pct_encoded_view.hpp>
  16 |+ #include <boost/url/decode_view.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:21:52 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954487251  

I do try to keep these in alphabetical order so they are easier to find and read


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 04:25:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1084766267  

📁 include/boost/url/query_param.hpp

```diff
 176 |-                 pct_encoded_view(value),
 175 |+                 decode_view(key),
 176 |+                 decode_view(value),
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:25:54 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954488704  

Something isn't right here, because `decode_view` will do validation and throw on invalid input. But if this object is being constructed by the container, we already know it is valid. Furthermore, the constructor for `decode_view` can throw, but is being called inside a `noexcept` function.

> Username: alandefreitas  
> Created_at: 2022-08-25 17:17:00 UTC  
> Updated_at: 2022-08-25 17:18:19 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955232023  

Yes. We have many issues here. If we want to ensure this is only created by the container, then we need to hide the public constructors that don't validate things. If we want to the user to be able to create this, then we need to either validate the input when constructing it or remove `noexcept` from the conversion.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 04:26:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1084766551  

📁 include/boost/url/query_param.hpp

```diff
 302 |-                 pct_encoded_view(value),
 301 |+                 decode_view(key),
 302 |+                 decode_view(value),
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:26:26 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954488915  

same problem here where we are validating needlessly, and possibly throwing from a function marked `noexcept`


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 04:27:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1084767045  

📁 include/boost/url/src.hpp

```diff
  37 | #include <boost/url/detail/impl/params_iterator_impl.ipp>
  38 |- #include <boost/url/detail/impl/pct_encoded_view.ipp>
  38 |+ #include <boost/url/detail/impl/decode_view.ipp>
```

> Username: vinniefalco  
> Created_at: 2022-08-25 04:27:26 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r954489333  

This is no longer in alphabetical order


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 17:24:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085839018  

📁 doc/qbk/2.0.quicklook.qbk

```diff
 190 | 
 191 | [h3 Decoding]
```

> Username: vinniefalco  
> Created_at: 2022-08-25 17:24:15 UTC  
> Updated_at: 2022-08-25 17:24:52 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955238267  

You could call this `[heading Percent-Encoding]`


---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-08-25 17:30:29 UTC  
> Url: https://github.com/boostorg/url/pull/453#issuecomment-1227567555  

An automated preview of the documentation is available at [https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 17:33:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085851721  

📁 include/boost/url/grammar/all_chars.hpp

```diff
  74 |+ };
  75 |+ 
  76 |+ /** A character set containing the alphabetical characters.
```

> Username: vinniefalco  
> Created_at: 2022-08-25 17:33:39 UTC  
> Updated_at: 2022-08-25 17:33:40 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955246577  

This brief is wrong


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 17:34:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085853047  

📁 include/boost/url/grammar/all_chars.hpp

```diff
  77 |+ 
  78 |+     @see
  79 |+         @ref all_chars_t
```

> Username: vinniefalco  
> Created_at: 2022-08-25 17:34:48 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955247551  

You need Example and Specification sections, plus additional see-alsos. Copy from another charset:   
  
https://github.com/CPPAlliance/url/blob/954eb5b7e6af8228fa3b8a644c6a2e51b29980e7/include/boost/url/grammar/alpha_chars.hpp#L20

> Username: alandefreitas  
> Created_at: 2022-08-25 18:40:44 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955301111  

The only difference between the two is:  
  
```  
    @par Specification  
    @li <a href="https://datatracker.ietf.org/doc/html/rfc5234#appendix-B.1"  
        >B.1. Core Rules (rfc5234)</a>  
```  
  
which seems correct, since there's no specification for `all_chars`


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 17:38:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085858468  

📁 include/boost/url/impl/decode.hpp

```diff
  46 |+     while(it != end)
  47 |+     {
  48 |+         if( ! opt.allow_null &&
```

> Username: vinniefalco  
> Created_at: 2022-08-25 17:38:50 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955251046  

shouldn't the decision to allow null be in the CharSet? Otherwise we can't use `find_if_not`...

> Username: alandefreitas  
> Created_at: 2022-08-25 18:46:47 UTC  
> Updated_at: 2022-08-25 18:47:06 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955306114  

It can also be in the charset. The difference is `allow_null` will check if this is null before and after decoding, while removing it from the charset only forbids it before decoding. So we would still need the option `allow_null` to be available.  
  
We don't use  `find_if_not` but we could use it to find the next '%'/'\0' and memcpy the rest. I don't know how much we would gain by doing that, but I think it's worth investing more time on the API for now.

> Username: vinniefalco  
> Created_at: 2022-08-25 18:50:37 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955309609  

Right, I'm not saying to use `find_if_not` now but I am pointing out that if we want to use it in the future we need to be careful with the API


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 17:39:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085859064  

📁 include/boost/url/grammar/all_chars.hpp

```diff
  50 |+     operator()(char c) const noexcept
  51 |+     {
  52 |+         return c != '%';
```

> Username: vinniefalco  
> Created_at: 2022-08-25 17:39:19 UTC  
> Updated_at: 2022-08-25 17:39:20 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955251466  

you can't do this in a type named `all_chars_t`

> Username: vinniefalco  
> Created_at: 2022-08-25 17:39:34 UTC  
> Updated_at: 2022-08-25 17:39:35 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955251678  

Maybe we should do this instead:  
```  
template< class CharSet =  
#ifdef BOOST_URL_DOCS  
    __implementation_defined__  
#else  
    detail::decode_chars_t  
#endif  
>  
auto decode(...  
```

> Username: alandefreitas  
> Created_at: 2022-08-25 18:51:26 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955310243  

Yes. I'll fix `decode` already.


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 18:47:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085940731  

📁 example/magnet/magnet.cpp

```diff
 567 | 
 568 |- urls::optional<urls::pct_encoded_view>
 568 |+ urls::optional<urls::decode_view>
```

> Username: vinniefalco  
> Created_at: 2022-08-25 18:47:48 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955306951  

outside of the `urls` namespace you should probably say `boost::optional`


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 18:48:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085941500  

📁 include/boost/url/encode.hpp

```diff
  14 |+ #include <boost/url/detail/config.hpp>
  15 |+ #include <boost/url/string_view.hpp>
  16 |+ #include <boost/url/encode_opts.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-08-25 18:48:28 UTC  
> Updated_at: 2022-08-25 18:48:29 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955307412  

alphabetical


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 18:53:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085948709  

📁 include/boost/url/impl/url_base.ipp

```diff
 280 |     auto const n =
 281 |-         detail::pct_encode_bytes_impl(
 281 |+         detail::encoded_size_impl(
```

> Username: vinniefalco  
> Created_at: 2022-08-25 18:53:44 UTC  
> Updated_at: 2022-08-25 18:53:45 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955312109  

Why aren't we using the public function?

> Username: alandefreitas  
> Created_at: 2022-08-25 19:08:38 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955324232  

Same as the other one. We don't have an overload for arbitrary iterators and we can't use `s.encoded().size()` because it's a different `allowed` charset.

> Username: vinniefalco  
> Created_at: 2022-08-25 19:10:19 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955325646  

So `s` is `decode_view` here?

> Username: alandefreitas  
> Created_at: 2022-08-25 19:23:24 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955340783  

Yes. This is the `decode_view` overload.


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 18:55:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085951907  

📁 include/boost/url/impl/url_base.ipp

```diff
 434 |         s, detail::password_chars, {});
 435 |     if( !rn )
 436 |         detail::throw_invalid_argument();
```

> Username: vinniefalco  
> Created_at: 2022-08-25 18:55:14 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955313291  

I don't understand, why aren't we using `decode` which returns `result<decode_view>`? We could write the whole thing like this:  
```  
auto ds = decode( s, detail::password_chars ).value();  
```  
and get the throw for free.

> Username: vinniefalco  
> Created_at: 2022-08-25 19:14:03 UTC  
> Updated_at: 2022-08-25 19:14:04 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955330210  

Oh... `s` is `decode_view`...


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 18:58:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085956006  

📁 test/unit/encode.cpp

```diff
   9 |+ 
  10 |+ // Test that header file is self-contained.
  11 |+ #include <boost/url/grammar/lut_chars.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-08-25 18:58:09 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955316249  

Wrong header this should be encode.hpp


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 18:58:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085956115  

📁 test/unit/encode.cpp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2019 Vinnie Falco (vinnie.falco@gmail.com)
```

> Username: vinniefalco  
> Created_at: 2022-08-25 18:58:15 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955316338  

your name please


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 19:11:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085981131  

📁 include/boost/url/impl/url_base.ipp

```diff
 276 |         BOOST_URL_STACK_BYTES> buf(
 277 |             this->string());
 278 |     s = s.maybe_copy(buf);
```

> Username: vinniefalco  
> Created_at: 2022-08-25 19:11:08 UTC  
> Updated_at: 2022-08-25 19:11:09 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955326641  

`copied_strings` knows about `decode_view`?

> Username: vinniefalco  
> Created_at: 2022-08-25 19:12:04 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955327738  

oh the other way around, `decode_view` has a `maybe_copy` that knows about `copied_strings`

> Username: alandefreitas  
> Created_at: 2022-08-25 19:17:59 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955334841  

Yes. You implemented it. :)

> Username: vinniefalco  
> Created_at: 2022-08-25 19:36:17 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955351913  

lmao


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 19:13:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1085983988  

📁 include/boost/url/impl/url_base.ipp

```diff
 307 |     auto const rn =
 308 |-         validate_pct_encoding(
 308 |+         detail::validate_encoding(
```

> Username: vinniefalco  
> Created_at: 2022-08-25 19:13:22 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955329447  

Why can't this use the public `pct_decode` ?


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-25 19:26:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/453#pullrequestreview-1086000697  

📁 include/boost/url/detail/impl/any_path_iter.ipp

```diff
  78 |-     auto rn = urls::validate_pct_encoding(
  79 |-         s, pchars, {});
  79 |+     auto rn = urls::decode(s, {}, pchars);
```

> Username: vinniefalco  
> Created_at: 2022-08-25 19:26:55 UTC  
> Url: https://github.com/boostorg/url/pull/453#discussion_r955344722  

does not need namespace qualifier


---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2022-08-25 19:35:25 UTC  
> Url: https://github.com/boostorg/url/pull/453#issuecomment-1227681670  

An automated preview of the documentation is available at [https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://453.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 29

> Username: alandefreitas  
> Created_at: 2022-08-25 20:41:12 UTC  
> Url: https://github.com/boostorg/url/pull/453#issuecomment-1227741618  

Merged

---
