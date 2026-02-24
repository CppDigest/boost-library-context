# #238 replace const_string [Closed]

> Username: alandefreitas  
> Created at: 2022-07-19 23:17:51 UTC  
> Updated at: 2022-08-03 21:06:55 UTC  
> Closed at: 2022-07-27 21:21:30 UTC  
> Url: https://github.com/boostorg/url/pull/238  

fix #235, fix #223, fix #168, fix #164, fix #163, fix #159  
  
The tasks from #235 are split into separate [commits](https://github.com/CPPAlliance/url/pull/238/commits).  
  
- Root out const_string, ✅  
- ~~Cut half of the segments/params containers out~~,   
- ~~Cut out half of the observers~~,   
- Change the `pct_encoded_rule` to use the view, and ✅  
- ~~Change the pct_encoding API per the issue~~ (fixed in https://github.com/CPPAlliance/url/pull/234)  
- Adapt url and url member functions  
    - add `set_XXXXX(pct_encoded_view)` ✅  
    - `XXXXX()` can become `noexcept`: we need a private constructor that lets us bypass validation ✅  
- Review javadocs ✅  
- Review docs ✅  
- Check coverage

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-07-21 06:10:25 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1191080094  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-07-21 09:06:21 UTC  
> Updated_at: 2022-07-26 20:19:20 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1191236604  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/238?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#238](https://codecov.io/gh/CPPAlliance/url/pull/238?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (6ce5a0e) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/ef2db3c1e52474b881812da92ae495cd936ed150?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (ef2db3c) will **decrease** coverage by `0.25%`.  
> The diff coverage is `94.58%`.  
  
> :exclamation: Current head 6ce5a0e differs from pull request most recent head 2667318. Consider uploading reports for the commit 2667318 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/238/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/238?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #238      +/-   ##  
===========================================  
- Coverage    96.78%   96.52%   -0.26%       
===========================================  
  Files          120      121       +1       
  Lines         6068     6305     +237       
===========================================  
+ Hits          5873     6086     +213       
- Misses         195      219      +24       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/238?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/any\_path\_iter.hpp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9wYXRoX2l0ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/copied\_strings.hpp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2NvcGllZF9zdHJpbmdzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [...t/url/detail/impl/params\_encoded\_iterator\_impl.ipp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGFyYW1zX2VuY29kZWRfaXRlcmF0b3JfaW1wbC5pcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/impl/pct\_encoding.ipp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGN0X2VuY29kaW5nLmlwcA==) | `62.16% <0.00%> (ø)` | |  
| [include/boost/url/detail/normalize.hpp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL25vcm1hbGl6ZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [.../boost/url/detail/params\_encoded\_iterator\_impl.hpp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3BhcmFtc19lbmNvZGVkX2l0ZXJhdG9yX2ltcGwuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/params\_iterator\_impl.hpp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3BhcmFtc19pdGVyYXRvcl9pbXBsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [...nclude/boost/url/detail/segments\_iterator\_impl.hpp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3NlZ21lbnRzX2l0ZXJhdG9yX2ltcGwuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/ipv4\_address.ipp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9pcHY0X2FkZHJlc3MuaXBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/ipv6\_address.ipp](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9pcHY2X2FkZHJlc3MuaXBw) | `100.00% <ø> (ø)` | |  
| ... and [74 more](https://codecov.io/gh/CPPAlliance/url/pull/238/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/238?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/238?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [c899642...2667318](https://codecov.io/gh/CPPAlliance/url/pull/238?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-21 13:31:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1046536632  

📁 doc/qbk/2.0.quicklook.qbk

```diff
 288 | 
 289 |- All functions that return __const_string__ accept an optional __Allocator__.
 289 |+ All functions that return __pct_encoded_view__ accept an optional __Allocator__.
```

> Username: vinniefalco  
> Created_at: 2022-07-21 13:31:19 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r926679415  

There is no optional allocator anymore


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-21 13:31:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1046537416  

📁 doc/qbk/3.0.parsing.qbk

```diff
  70 | ][
  71 |-     [`mailto:mduerst@ifi.unizh.ch`]
  71 |+     [[teletype]`mailto:mduerst@ifi.unizh.ch`]
```

> Username: vinniefalco  
> Created_at: 2022-07-21 13:31:48 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r926679936  

No no, you can't use a real email address! Use `person@example.com`

> Username: alandefreitas  
> Created_at: 2022-07-22 02:07:50 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927230160  

Weird. I didn't come up with that. That came from the RFC or something.


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-07-22 01:29:55 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1192088320  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-07-22 01:59:56 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1192101976  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-07-22 02:09:55 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1192106270  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-07-22 02:14:56 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1192108624  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:25:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047313504  

📁 include/boost/url/authority_view.hpp

```diff
 537 |-         Allocator const& a = {}) const
 533 |+     pct_encoded_view
 534 |+     userinfo() const
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:25:38 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927236306  

`noexcept`?


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:26:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047314124  

📁 include/boost/url/authority_view.hpp

```diff
 551 |-             });
 538 |+         return pct_encoded_view(
 539 |+             encoded_userinfo(), opt);
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:26:55 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927236815  

We should open an issue and note that this function could use the `decoded_` array information


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:27:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047314258  

📁 include/boost/url/authority_view.hpp

```diff
 640 |-         Allocator const& a = {}) const
 623 |+     pct_encoded_view
 624 |+     user() const
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:27:12 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927236915  

`noexcept`?


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:27:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047314378  

📁 include/boost/url/authority_view.hpp

```diff
 654 |-             });
 628 |+         return pct_encoded_view(
 629 |+             encoded_user(), opt);
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:27:29 UTC  
> Updated_at: 2022-07-22 02:29:21 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927236997  

Don't we have `decoded_[id_user]`? Should we use `detail::access::construct()`?


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:30:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047315641  

📁 include/boost/url/detail/impl/pct_encoded_view.ipp

```diff
  29 |+     pct_decode_opts const& opt) noexcept
  30 |+ {
  31 |+     return pct_encoded_view(s, n, opt);
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:30:05 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927237905  

This should not be out of line, its just a one-liner.

> Username: alandefreitas  
> Created_at: 2022-07-23 01:31:56 UTC  
> Updated_at: 2022-07-23 01:31:57 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928061905  

That would create cyclic dependency on pct_encoded_view  
  
```  
error: return type ‘class boost::urls::pct_encoded_view’ is incomplete  
```

> Username: vinniefalco  
> Created_at: 2022-07-23 01:46:50 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928063409  

There's ways around that. Make a note (or issue) and we can explore it later.


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:30:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047315777  

📁 include/boost/url/detail/impl/pct_encoding.ipp

```diff
  25 |     string_view plain_key,
  26 |-     pct_encoded_str encoded) noexcept
  26 |+     pct_encoded_view encoded) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:30:22 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927237995  

we can just rename the parameter to `s` and that will make this much more readable


---

## Comment 15

> Username: alandefreitas  
> Created_at: 2022-07-22 02:31:43 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1192116439  

In the tests, we have a situation where  
  
```  
u.set_user(s);  
```  
  
works for `string_view` (encoding whatever we have to) but doesn't work for a `pct_encoded_view` representing/dereferencing to the same decoded string because `s.encoded()` might not have been created with the correct characters for that component.  
  
One thing we will probably have to discuss is the semantics of set_XXXXXX(pct_encoded_view).  
  
- Getting inspiration from set_XXXXXX(string_view) seems to make sense because the iterators dereference to the decoded chars.  
- Getting inspiration from set_encoded_XXXXXX(string_view) also seems to make sense because it stores the encoded string so we just have set_encoded_XXXX(s.encoded()).  
  
Another alternative is to make their behavior uniform in regards to invalid chars. That is, `set_XXXXX(pct_encoded_view)` and `set_encoded_XXXXX(string_view)` would encode anything that happens to be outside the charset instead of throwing. The main difference would be how they treat "%"s. This way, `set_XXXXX(pct_encoded_view s)` could just be defined as `set_encoded_XXXXX(s.encoded())`.

---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:32:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047316732  

📁 include/boost/url/detail/params_iterator_impl.hpp

```diff
  45 |-         int,
  46 |-         const_string::factory a) noexcept;
  43 |+         int) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:32:31 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927238742  

This is confusing. If you have `nparam` why do you also need the dummy `int`? The begin constructor takes only `string_view`.


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:33:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047317225  

📁 include/boost/url/detail/pct_encoded_view.hpp

```diff
  18 |+ namespace detail {
  19 |+ 
  20 |+ struct unchecked_encoded_view
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:33:34 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927239094  

oh boy these long identifiers... You can just rename this to `access`. And if we need to add any more functions to it, we can just put them all in here, even if they related to different classes.


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:33:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047317327  

📁 include/boost/url/detail/pct_encoded_view.hpp

```diff
  21 |+ {
  22 |+     // construct unchecked pct_encoded_view
  23 |+     BOOST_URL_DECL
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:33:49 UTC  
> Updated_at: 2022-07-22 02:33:50 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927239167  

This function should be inline since it is a one-liner

> Username: alandefreitas  
> Created_at: 2022-07-23 01:32:15 UTC  
> Updated_at: 2022-07-23 01:32:16 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928061933  

That would create cyclic dependency on pct_encoded_view  
  
```  
error: return type ‘class boost::urls::pct_encoded_view’ is incomplete  
```


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:35:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047317888  

📁 include/boost/url/impl/params.hpp

```diff
  41 |-         const_string::factory a) noexcept
  42 |-         : impl_(s, nparam, 0, a)
  39 |+         int) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:35:04 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927239602  

same thing here why do we need the dummy int


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:36:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047318377  

📁 include/boost/url/impl/pct_encoded_view.ipp

```diff
  40 |+ // unchecked constructor
  41 |+ pct_encoded_view::
  42 |+ pct_encoded_view(
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:36:00 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927239959  

This one can be out of line, with DLL linkage

> Username: alandefreitas  
> Created_at: 2022-07-23 01:32:22 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928061970  

Isn't it already out of line with DLL linkage?

> Username: vinniefalco  
> Created_at: 2022-07-23 01:54:38 UTC  
> Updated_at: 2022-07-23 01:55:11 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928064138  

I was just agreeing that this one is good the way it is, after I pointed out the previous two constructors should have been inline (but we discovered it caused a cycle).

> Username: alandefreitas  
> Created_at: 2022-07-23 23:17:25 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928174458  

Oh OK.


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 02:37:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047319166  

📁 include/boost/url/impl/segments.hpp

```diff
  57 |-     using value_type = const_string;
  58 |-     using reference = const_string;
  55 |+     using value_type = pct_encoded_view;
```

> Username: vinniefalco  
> Created_at: 2022-07-22 02:37:48 UTC  
> Updated_at: 2022-07-22 02:38:22 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927240564  

Well no you can't have a reference as the `value_type`. This expression must be valid:  
  
```  
segments segs = ...,;  
  
segments::value_type s( *segs.begin() );  
```  
  
At this point `s` should have ownership of a copy of the decoded segment. That means that `value_type` has to be `std::string`, or something like it.  
  
`std::is_convertible< reference, value_type >::value` must be `true`.

> Username: alandefreitas  
> Created_at: 2022-07-23 01:32:40 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928062009  

> std::string, or something like it.  
  
We don't have `const_string` anymore, so `std::string`?   
  
Are we returning with the allocator parameter for `url_view::segments` then?  
  
> Well no you can't have a reference as the value_type  
  
That's not necessarily a usual requirement of views / or views of views.

> Username: vinniefalco  
> Created_at: 2022-07-23 01:47:58 UTC  
> Updated_at: 2022-07-23 01:55:50 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928063501  

> We don't have const_string anymore, so std::string?  
  
Yes  
  
> Are we returning with the allocator parameter for url_view::segments then?  
  
No  
  
`operator*` should return `reference`, and `value_type` should be constructible from `reference` and have value semantics.

> Username: alandefreitas  
> Created_at: 2022-07-23 23:25:33 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928174951  

OK. I changed that. But  
  
```cpp  
segments::value_type s( *segs.begin() );  
```  
  
still won't work.   
  
We would need  
  
```cpp  
segments::value_type s( (*segs.begin()).to_string() );  
```  
  
unless we return with the `explicit` `operator basic_string` you told me to remove.

> Username: vinniefalco  
> Created_at: 2022-07-23 23:45:01 UTC  
> Updated_at: 2022-07-23 23:45:02 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928176071  

Yep... we might have to put that back unfortunately.


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:16:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047364357  

📁 include/boost/url/impl/segments_view.hpp

```diff
 128 |- }
 129 |- 
 115 | inline
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:16:42 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927274405  

Does `inline` mean anything in the declaration?


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:17:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047364862  

📁 include/boost/url/impl/url.ipp

```diff
 357 |+ url&
 358 |+ url::
 359 |+ set_scheme(pct_encoded_view s)
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:17:39 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927274756  

Umm... schemes are never encoded. This should be `string_view` only.

> Username: alandefreitas  
> Created_at: 2022-07-23 01:33:10 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928062057  

`s` dereferences to decoded characters

> Username: vinniefalco  
> Created_at: 2022-07-23 01:48:22 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928063559  

it doesn't matter, `pct_encoded_view` makes no sense here

> Username: alandefreitas  
> Created_at: 2022-07-24 00:39:40 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928179434  

Yes. I've already removed it.


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:19:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047365707  

📁 include/boost/url/impl/url.ipp

```diff
 615 |+     {
 616 |+         set_user(s.substr(0, sep));
 617 |+         set_password(s.substr(sep + 1));
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:19:20 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927275360  

umm... this doesn't give the strong exception safety guarantee. You have to do the entire operation in a single resize. So you can't call the public-facing user functions. You need to call `resize_impl` (I think) and then write the encoded characters into that buffer. And then set `decoded_` correctly based on `sep`.

> Username: vinniefalco  
> Created_at: 2022-07-22 04:26:33 UTC  
> Updated_at: 2022-07-22 04:26:34 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927278253  

Actually this is incorrect. `set_user` (line 611) will encode the colon and that's not what we want. Userinfo allows colon:  
```  
userinfo    = *( unreserved / pct-encoded / sub-delims / ":" )  
```  
  
so if they call `set_userinfo` with a colon, then a colon should appear in the string returned by `encoded_userinfo`. This function is actually very simple. Calculate the encoded size of `s` using the `userinfo_chars` set from above, call `resize_impl` on `[id_user, id_pass)` with that size (plus two for the double slashes if necessary), output the double slash (if needed) followed by percent-encoded chars, and then use `split` to cut the encoded string at the first colon.

> Username: alandefreitas  
> Created_at: 2022-07-23 01:33:34 UTC  
> Updated_at: 2022-07-23 01:33:35 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928062095  

> You have to do the entire operation in a single resize  
  
That makes sense.  
  
I've updated the code to have the strong exception safety guarantee.  
  
> This function is actually very simple  
  
It was a little more than that but it worked  
  
> set_user (line 611) will encode the colon  
  
It won't. The caller never gives it a substr with the colon.  
  
> a colon should appear in the string returned by encoded_userinfo  
  
It will.  
  
> then use split to cut the encoded string  
  
_only_ `split` is problematic as it is because it breaks `decoded_`


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:20:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047366201  

📁 include/boost/url/impl/url.ipp

```diff
 530 |+ url&
 531 |+ url::
 532 |+ set_password(pct_encoded_view s)
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:20:13 UTC  
> Updated_at: 2022-07-22 04:20:26 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927275743  

Honestly I thought we were doing  
```  
set_encoded_password( pct_encoded_view )  
```  
not  
```  
set_password( pct_encoded_view )  
```  
  
There is a little bit of confusion here and I'm not sure what is right.

> Username: alandefreitas  
> Created_at: 2022-07-23 01:34:01 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928062135  

Your original [comment](https://cpplang.slack.com/archives/C01JR6C9C4U/p1658262764343469) referred to  
  
> I'm open to 3 functions instead of 2 or 1:  
  
```cpp  
url& url::set_encoded_X( string_view );  
url& url::set_X( string_view );  
url& url::set_X( pct_encoded_view const& );  
```  
  
I agree with that original view. It makes much more sense semantically, and the other member functions make much more sense:  
  
```cpp  
u1.set_user(u2.user());  
```  
  
instead of   
  
```cpp  
u1.set_encoded_user(u2.user());  
// people will be confused: how are these not symmetrical / can we set encoded with a decoded result  
```  
  
and also, of course, the fact that `pct_encoded_view` dereferences to decoded characters.


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:21:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047366977  

📁 include/boost/url/impl/url.ipp

```diff
 908 |+ url&
 909 |+ url::
 910 |+ set_port(pct_encoded_view s)
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:21:29 UTC  
> Updated_at: 2022-07-22 04:21:30 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927276255  

You did more work than necessary.. port can't be encoded:  
```  
port        = *DIGIT  
```  
  
Did we even have `set_port( string_view )` before?

> Username: alandefreitas  
> Created_at: 2022-07-23 01:34:14 UTC  
> Updated_at: 2022-07-23 01:34:25 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928062176  

I removed `set_port(pct_encoded_view s);`  
  
> Did we even have set_port( string_view ) before?  
  
Yes. `set_port( string_view )` should be useful. Ports in Asio applications are often (always?) strings.

> Username: vinniefalco  
> Created_at: 2022-07-23 01:49:47 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928063702  

Fair, but note that the string passed to `set_port( string_view )` must only contain digits (or be empty).


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:29:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047371259  

📁 include/boost/url/impl/url.ipp

```diff
 626 |+     pct_encoded_view s)
 627 |+ {
 628 |+     return set_userinfo(s.encoded());
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:29:33 UTC  
> Updated_at: 2022-07-22 04:29:44 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927279267  

First of all shouldn't this be `set_encoded_userinfo`? Because `s.encoded()` can have escapes, and then you will be escaping the escapes, which is wrong.  
  
Second, I think that all these places where we are just passing `s.encoded()` are probably incorrect, because there is no guarantee that the CharSet used to percent-encode `s` is the same as the CharSet which is required for the component that we are setting. These functions need to behave as-if the passed encoded string is first decoded, and then re-encoded using the applicable CharSet.  
  
To simplify things before the review, I am ok with removing ALL `set` overloads which accept `pct_encoded_view`, and just opening an issue with a note that we should explore it in the future.

> Username: alandefreitas  
> Created_at: 2022-07-23 01:36:12 UTC  
> Updated_at: 2022-07-23 01:36:13 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928062337  

> First of all shouldn't this be set_encoded_userinfo?  
  
Replied in the previous [comment](https://github.com/CPPAlliance/url/pull/238#discussion_r927275743)  
  
> these places where we are just passing s.encoded() are probably incorrect  
  
You're basically asking me what I've been asking for a while but everyone has ignored it.  
  
The last comment where I mention this is [here](https://github.com/CPPAlliance/url/pull/238#issuecomment-1192116439)  
  
I have some opinions on what the function should do but we don't often reach the same conclusions, so I preferred to ask.

> Username: vinniefalco  
> Created_at: 2022-07-23 01:50:37 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928063776  

Hmm we will have to sort this 2ma

> Username: vinniefalco  
> Created_at: 2022-07-23 01:56:57 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928064390  

> You're basically asking me what I've been asking for a while but everyone has ignored it.  
  
I didn't see it until I actually had code in front of me


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:30:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047371659  

📁 include/boost/url/impl/url.ipp

```diff
 643 |         detail::throw_invalid_argument(
 644 |             BOOST_CURRENT_LOCATION);
 645 |     auto dest = set_userinfo_impl(s.size());
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:30:21 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927279561  

oh nice we already have a `set_userinfo_impl`, you can just use this in `set_userinfo`. And this code does the split too.


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:33:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047372932  

📁 include/boost/url/impl/url_view.ipp

```diff
 656 |             t.port.port.size() + 1);
 639 |- 
 657 |+         decoded_[id_port] = t.port.port.size();
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:33:05 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927280627  

port is only digits so `decoded_[id_port]` should always be zero


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:34:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047373401  

📁 include/boost/url/params.hpp

```diff
  51 |-         url& u,
  52 |-         Allocator const& a);
  47 |+     params(url& u)
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:34:05 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927280983  

`noexcept`?


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:34:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047373646  

📁 include/boost/url/pct_encoded_view.hpp

```diff
  74 |     using traits_type = std::char_traits<char>;
  75 | 
  76 |+     friend detail::unchecked_encoded_view;
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:34:36 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927281168  

`friend detail::access`


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:35:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047373951  

📁 include/boost/url/pct_encoded_view.hpp

```diff
1061 | } // urls
1062 |+ 
1063 |+ namespace filesystem {
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:35:18 UTC  
> Updated_at: 2022-07-22 04:35:19 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927281395  

This needs `#ifndef BOOST_URL_DOCS`


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:36:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047374381  

📁 include/boost/url/rfc/impl/host_rule.ipp

```diff
  32 |             urls::host_type::name;
  33 |-         t.name = {};
  33 |+         t.name = pct_encoded_view("");
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:36:13 UTC  
> Updated_at: 2022-07-22 04:36:14 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927281690  

no default constructor?


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:37:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047374868  

📁 include/boost/url/url.hpp

```diff
 381 |+     BOOST_URL_DECL
 382 |+     url&
 383 |+     set_scheme(pct_encoded_view s);
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:37:15 UTC  
> Updated_at: 2022-07-22 04:37:16 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927282048  

schemes aren't encoded


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:38:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047375243  

📁 include/boost/url/url.hpp

```diff
 636 |+         the separator between the user and the
 637 |+         password fields. Other occurrences of
 638 |+         the separator character will be encoded.
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:38:03 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927282353  

This is not correct. `set_userinfo` _never_ encodes colons.


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:38:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047375714  

📁 include/boost/url/url.hpp

```diff
 642 |+         @ref set_password should be used
 643 |+         separately so that the character can be
 644 |+         unambiguously encoded.
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:38:59 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927282708  

This doc needs to explain that the interpretation of `userinfo` as individual `user` and `pass` is scheme-dependent. And that using a password is deprecated.


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:40:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047376186  

📁 include/boost/url/url.hpp

```diff
 946 |     url&
 947 |     set_port(string_view s);
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:40:04 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927283106  

This string can contain only digits

> Username: alandefreitas  
> Created_at: 2022-07-23 01:36:19 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928062360  

See https://github.com/CPPAlliance/url/pull/238#discussion_r927276255


---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:40:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047376238  

📁 include/boost/url/url.hpp

```diff
 949 |+     /// @copydoc set_port(string_view)
 950 |+     url&
 951 |+     set_port(pct_encoded_view s);
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:40:10 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927283151  

ports aren't encoded

> Username: alandefreitas  
> Created_at: 2022-07-23 01:36:26 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928062371  

Removed.  
  
`pct_encoded_view` deferences to decoded chars though.


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:41:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047376696  

📁 include/boost/url/url_view.hpp

```diff
1424 |+ 
1425 |+         @par Exception Safety
1426 |+         Calls to allocate may throw.
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:41:10 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927283486  

Throws nothing


---

## Review 40 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:41:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047376736  

📁 include/boost/url/url_view.hpp

```diff
1426 |+         Calls to allocate may throw.
1427 |+ 
1428 |+         @param a An optional allocator the returned
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:41:16 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927283507  

There's no allocator


---

## Review 41 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-22 04:41:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1047376835  

📁 include/boost/url/url_view.hpp

```diff
1430 |+         the default allocator is used
1431 |+ 
1432 |+         @return A @ref pct_encoded_view.
```

> Username: vinniefalco  
> Created_at: 2022-07-22 04:41:26 UTC  
> Updated_at: 2022-07-22 04:41:27 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r927283554  

I'm not sure I like repeating the return type


---

## Comment 42

> Username: cppalliance-bot  
> Created_at: 2022-07-23 01:39:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193034043  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 43 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-23 01:51:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1048489285  

📁 include/boost/url/authority_view.hpp

```diff
 521 |-         @return A @ref const_string using the
 522 |+         @return A @ref pct_encoded_view using the
 523 |         specified allocator.
```

> Username: vinniefalco  
> Created_at: 2022-07-23 01:51:44 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928063867  

There's no allocator, and I don't think I like repeating the return type.


---

## Review 44 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-23 01:52:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1048489307  

📁 include/boost/url/authority_view.hpp

```diff
 550 |-                     dest, dest + n, s, opt);
 551 |-             });
 538 |+         return detail::access::construct(
```

> Username: vinniefalco  
> Created_at: 2022-07-23 01:52:00 UTC  
> Updated_at: 2022-07-23 01:52:01 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928063881  

this looks very nice now


---

## Review 45 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-23 01:52:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1048489339  

📁 include/boost/url/authority_view.hpp

```diff
 622 |-         @return A @ref const_string using the
 613 |+         @return A @ref pct_encoded_view using the
 614 |         specified allocator.
```

> Username: vinniefalco  
> Created_at: 2022-07-23 01:52:22 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928063925  

no allocator


---

## Review 46 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-23 01:52:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1048489360  

📁 include/boost/url/authority_view.hpp

```diff
 722 |         allocator is used, and the return type of
 723 |         the function becomes `std::string`.
```

> Username: vinniefalco  
> Created_at: 2022-07-23 01:52:39 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r928063943  

There is no allocator and no `basic_string`


---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2022-07-24 03:19:52 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193237869  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 48

> Username: cppalliance-bot  
> Created_at: 2022-07-24 04:44:55 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193246108  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2022-07-24 04:49:53 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193246453  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 50

> Username: cppalliance-bot  
> Created_at: 2022-07-24 08:24:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193271272  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 51

> Username: cppalliance-bot  
> Created_at: 2022-07-24 08:39:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193273424  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 52

> Username: cppalliance-bot  
> Created_at: 2022-07-24 08:44:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193274187  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 53

> Username: cppalliance-bot  
> Created_at: 2022-07-24 09:04:55 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193276933  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 54

> Username: cppalliance-bot  
> Created_at: 2022-07-24 09:14:56 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193278508  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 55

> Username: cppalliance-bot  
> Created_at: 2022-07-24 09:29:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193280753  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 56

> Username: cppalliance-bot  
> Created_at: 2022-07-24 09:44:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193283039  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 57

> Username: cppalliance-bot  
> Created_at: 2022-07-24 10:09:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193286593  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 58

> Username: cppalliance-bot  
> Created_at: 2022-07-24 10:24:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193288995  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 59

> Username: cppalliance-bot  
> Created_at: 2022-07-24 11:04:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193294616  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 60

> Username: cppalliance-bot  
> Created_at: 2022-07-24 11:14:55 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193296170  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 61

> Username: cppalliance-bot  
> Created_at: 2022-07-24 11:29:54 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1193298482  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 62

> Username: cppalliance-bot  
> Created_at: 2022-07-25 23:59:56 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1194792929  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 63

> Username: cppalliance-bot  
> Created_at: 2022-07-26 00:05:00 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1194796164  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 64

> Username: cppalliance-bot  
> Created_at: 2022-07-26 00:10:00 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1194799955  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 65 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-26 01:22:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/238#pullrequestreview-1050258401  

📁 include/boost/url/impl/pct_encoding.hpp

```diff
 198 |+ template<class CharSet>
 199 |+ std::size_t
 200 |+ pct_encode_bytes(
```

> Username: vinniefalco  
> Created_at: 2022-07-26 01:22:06 UTC  
> Updated_at: 2022-07-26 01:22:07 UTC  
> Url: https://github.com/boostorg/url/pull/238#discussion_r929442878  

I think this needs to be called `pct_encoded_bytes`?


---

## Comment 66

> Username: vinniefalco  
> Created_at: 2022-07-26 01:24:27 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1194862428  

good work so far

---

## Comment 67

> Username: cppalliance-bot  
> Created_at: 2022-07-26 20:24:57 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1195942561  

An automated preview of the documentation is available at [https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://238.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 68

> Username: alandefreitas  
> Created_at: 2022-07-27 21:21:30 UTC  
> Url: https://github.com/boostorg/url/pull/238#issuecomment-1197382093  

Merged at 5c307a889af674bcacb8ba4e5f15e1c9a949b6f9

---
