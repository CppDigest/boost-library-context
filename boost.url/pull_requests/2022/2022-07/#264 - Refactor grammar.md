# #264 Refactor grammar [Closed]

> Username: vinniefalco  
> Created at: 2022-07-28 02:51:15 UTC  
> Updated at: 2022-07-30 00:45:51 UTC  
> Closed at: 2022-07-28 18:33:14 UTC  
> Url: https://github.com/boostorg/url/pull/264  

The Rule concept is changed:  
  
* rules are stateful values  
* nested value_type holds the result of parsing  
* member function `Rule::parse` is the algorithm  
* parse returns `result<value_type>`  
  
And:  
  
* All rfc3986 rules are reimplemented  
* New grammar non-terminal elements introduced:  
  - char_rule  
  - not_empty_rule  
  - optional_rule  
  - sequence_rule  
  - variant_rule  
   
Not included:  
  
* Javadocs  
* Exposition  
* Examples

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-07-28 02:59:56 UTC  
> Url: https://github.com/boostorg/url/pull/264#issuecomment-1197596970  

An automated preview of the documentation is available at [https://264.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://264.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 2 [Approved]

> Username: alandefreitas  
> Created_at: 2022-07-28 06:06:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/url/pull/264#pullrequestreview-1053457904  

That's a much better pattern for the grammar.

📁 doc/qbk/6.2.customization.qbk

```diff
  20 | 
  21 | [snippet_customization_1]
```

> Username: alandefreitas  
> Created_at: 2022-07-28 04:04:05 UTC  
> Updated_at: 2022-07-28 06:06:55 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r931761682  

Does this still make sense without the paragraph? One snippet then the table?

> Username: vinniefalco  
> Created_at: 2022-07-28 13:40:23 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r932233202  

Basically I have to rewrite the entire section 6, so all these snippets are invalid. This will be in a subsequent PR.


📁 include/boost/url/grammar/type_traits.hpp

```diff
  17 | #include <type_traits>
  18 | 
  19 | namespace boost {
```

> Username: alandefreitas  
> Created_at: 2022-07-28 04:49:30 UTC  
> Updated_at: 2022-07-28 06:06:40 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r931778167  

`is_mutable_string` is used only in `pct_encoded_view`. And that's outside `grammar`.

> Username: vinniefalco  
> Created_at: 2022-07-28 13:38:50 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r932231345  

True but I plan on using this pattern for more things


📁 include/boost/url/rfc/impl/absolute_uri_rule.ipp

```diff
  27 |+     value_type t;
  28 |+ 
  29 |+     // scheme
```

> Username: alandefreitas  
> Created_at: 2022-07-28 05:16:33 UTC  
> Updated_at: 2022-07-28 06:06:07 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r931789529  

`sequence_t`?  
  
```cpp  
return sequence(scheme_part_rule, hier_part_rule, query_part_rule);  
```  
  
or   
  
```cpp  
auto r =  sequence(scheme_part_rule, hier_part_rule, query_part_rule);  
if (!r)  
    return r.error();  
std::tie(t.scheme_part, t.hier_part, t.query_part) = *r;  
return t;  
```

> Username: vinniefalco  
> Created_at: 2022-07-28 13:44:38 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r932238060  

Yes, this is what I was saying before, that these changes add technical debt, because I just refactored everything as quickly as possible, without an eye towards improving it. After we merge this I am going to do a pass through the implementation of these rules to see how they can be tidied up. And some of them will be made private.


📁 include/boost/url/rfc/impl/authority_rule.ipp

```diff
  43 |+         if(rv.has_value())
  44 |+         {
  45 |+             t.has_userinfo = true;
```

> Username: alandefreitas  
> Created_at: 2022-07-28 05:18:24 UTC  
> Updated_at: 2022-07-28 06:06:07 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r931790305  

There are lots of places where the library uses `has_xxxx`/`xxxxx` and other places where it uses `optional<xxxx>`. I don't know if there should be some convention for that.

> Username: vinniefalco  
> Created_at: 2022-07-28 14:21:44 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r932281898  

Yes we have to consider this. Please open an issue. However note that after I am done refactoring the rules, we may or may not have these bools. Or we might use the optional rule, which does this automatically.


📁 include/boost/url/rfc/impl/h16_rule.ipp

```diff
  27 |+     result<value_type>
  28 |+ {
  29 |+     // VFALCO Why was this here?
```

> Username: alandefreitas  
> Created_at: 2022-07-28 05:23:49 UTC  
> Updated_at: 2022-07-28 06:06:07 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r931792684  

`sequence(...)` ?

> Username: vinniefalco  
> Created_at: 2022-07-28 14:22:03 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r932282263  

Refactoring the implementation is out of scope for this PR


📁 include/boost/url/rfc/paths_rule.hpp

```diff
 194 |     using value_type =
 279 |-         pct_encoded_view;
 195 |+         grammar::range<
```

> Username: alandefreitas  
> Created_at: 2022-07-28 05:40:13 UTC  
> Updated_at: 2022-07-28 06:06:07 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r931800505  

If `uri_rule::value_type`,... are becoming `url_view`, shouldn't these become `segments_view`?

> Username: vinniefalco  
> Created_at: 2022-07-28 13:39:39 UTC  
> Url: https://github.com/boostorg/url/pull/264#discussion_r932232299  

why, yes :)  
  
(check the issue I opened)


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-07-28 10:13:04 UTC  
> Url: https://github.com/boostorg/url/pull/264#issuecomment-1197944699  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/264?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#264](https://codecov.io/gh/CPPAlliance/url/pull/264?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (f7cf656) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/5c307a889af674bcacb8ba4e5f15e1c9a949b6f9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (5c307a8) will **increase** coverage by `0.13%`.  
> The diff coverage is `95.05%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/264/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/264?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #264      +/-   ##  
===========================================  
+ Coverage    96.52%   96.66%   +0.13%       
===========================================  
  Files          121      110      -11       
  Lines         6305     6208      -97       
===========================================  
- Hits          6086     6001      -85       
+ Misses         219      207      -12       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/264?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/error.ipp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9lcnJvci5pcHA=) | `27.02% <ø> (-1.93%)` | :arrow_down: |  
| [include/boost/url/impl/pct\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wY3RfZW5jb2RlZF92aWV3LmhwcA==) | `97.95% <ø> (ø)` | |  
| [include/boost/url/impl/pct\_encoding.hpp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wY3RfZW5jb2RpbmcuaHBw) | `97.65% <ø> (ø)` | |  
| [include/boost/url/ipv4\_address.hpp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2NF9hZGRyZXNzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/ipv6\_address.hpp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaXB2Nl9hZGRyZXNzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfZW5jb2RlZF92aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| ... and [46 more](https://codecov.io/gh/CPPAlliance/url/pull/264/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/264?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/264?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [5c307a8...f7cf656](https://codecov.io/gh/CPPAlliance/url/pull/264?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---
