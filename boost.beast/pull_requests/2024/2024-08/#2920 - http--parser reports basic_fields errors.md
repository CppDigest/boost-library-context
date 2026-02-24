# #2920 http::parser reports basic_fields errors [Merged]

> Username: ashtum  
> Created at: 2024-08-17 13:10:17 UTC  
> Updated at: 2024-08-21 21:02:30 UTC  
> Merged at: 2024-08-21 20:52:16 UTC  
> Closed at: 2024-08-21 20:52:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2920  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-17 14:43:03 UTC  
> Updated_at: 2024-08-21 21:02:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#issuecomment-2294879122  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2920?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.98990%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 93.12%. Comparing base [(`848e206`)](https://app.codecov.io/gh/boostorg/beast/commit/848e20680fb374532a0c5816b79b38532e6da775?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e306445`)](https://app.codecov.io/gh/boostorg/beast/commit/e306445391e50dcb0817ae34fc02dea90094b2b4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2920?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fbasic_parser.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | 98.24% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2920/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2920      +/-   ##  
===========================================  
+ Coverage    93.07%   93.12%   +0.05%       
===========================================  
  Files          177      177                
  Lines        13709    13643      -66       
===========================================  
- Hits         12759    12705      -54       
+ Misses         950      938      -12       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2920?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fbasic_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `94.11% <ø> (ø)` | |  
| [include/boost/beast/http/detail/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fdetail%2Fbasic_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/detail/basic\_parser.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fdetail%2Fbasic_parser.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZXIuaXBw) | `88.30% <ø> (-0.44%)` | :arrow_down: |  
| [include/boost/beast/http/fields.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Ffields.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/impl/error.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Ferror.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `95.83% <100.00%> (+0.18%)` | :arrow_up: |  
| [include/boost/beast/http/impl/fields.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Ffields.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmhwcA==) | `97.56% <100.00%> (+0.48%)` | :arrow_up: |  
| [include/boost/beast/http/parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fparser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `86.95% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2920?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fbasic_parser.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `97.12% <98.24%> (+1.67%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2920/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2920?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2920?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [848e206...e306445](https://app.codecov.io/gh/boostorg/beast/pull/2920?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:06:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248117694  

📁 include/boost/beast/http/fields.hpp

```diff
 446 |         @param value The value of the field, as a @ref boost::beast::string_view
 447 |+ 
 448 |+         @throws boost::system::system_error Thrown if an error occurs:
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:06:19 UTC  
> Updated_at: 2024-08-20 14:06:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723382274  

I thought the throws comes before the params?

> Username: ashtum  
> Created_at: 2024-08-20 14:12:33 UTC  
> Updated_at: 2024-08-20 14:12:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723393788  

Not in Beast source files at least.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:06:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248119412  

📁 include/boost/beast/http/fields.hpp

```diff
 451 |     */
 452 |     void
 453 |     insert(field name, string_view const& value);
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:06:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723383237  

string views are typically passed by value

> Username: ashtum  
> Created_at: 2024-08-20 14:09:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723388273  

Yes, I tried to be consistent with the existing overloads. should we change all of them?

> Username: vinniefalco  
> Created_at: 2024-08-20 17:41:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723707578  

Having both styles in the same parameter list is weird... I think we should change them in this file.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:07:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248121726  

📁 include/boost/beast/http/fields.hpp

```diff
 468 |+ 
 469 |+         @throws boost::system::system_error Thrown if an error occurs:
 470 |+         - If the size of @c value exceeds @ref max_value_size, the
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:07:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723384612  

Why the dash


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:07:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248122443  

📁 include/boost/beast/http/fields.hpp

```diff
 494 |+ 
 495 |+         @throws boost::system::system_error Thrown if an error occurs:
 496 |+         - If the size of @c name_string exceeds @ref max_name_size, the
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:07:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723385020  

should this use @li or is there a docca bug preventing it?

> Username: ashtum  
> Created_at: 2024-08-20 17:50:28 UTC  
> Updated_at: 2024-08-20 17:50:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723717049  

I replaced it with @li and it works fine.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:08:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248124106  

📁 include/boost/beast/http/fields.hpp

```diff
 533 |+ 
 534 |+     void
 535 |+     insert(field, string_view, std::nullptr_t, error_code& ec) = delete;
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:08:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723385961  

Why is there a `std::nullptr_t` param? And where's the doc for this?

> Username: ashtum  
> Created_at: 2024-08-20 14:16:28 UTC  
> Updated_at: 2024-08-20 14:42:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723400474  

prevent passing the literal 0: https://github.com/boostorg/beast/issues/2085


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:10:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248130868  

📁 include/boost/beast/http/impl/fields.hpp

```diff
 559 |+     field name,
 560 |+     string_view sname,
 561 |+     string_view const& value,
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:10:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723390164  

this is weird, why is this passed by reference and the other passed by value?

> Username: ashtum  
> Created_at: 2024-08-20 14:17:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723402906  

I didn't modify the existing signatures. Yes, they are strange, and we need to address them all.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:12:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248135913  

📁 include/boost/beast/http/fields.hpp

```diff
 748 |-     new_element(field name,
 749 |-         string_view sname, string_view value);
 810 |+     new_element(
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:12:10 UTC  
> Updated_at: 2024-08-20 14:12:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723392968  

oh wow.. two functions with the same name, one returning a pointer and the other returning a reference? lol... don't you think they should both return pointers?


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:13:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248140432  

📁 include/boost/beast/http/impl/fields.hpp

```diff
 593 |-     set_element(new_element(name, to_string(name),
 594 |-         static_cast<string_view>(value)));
 587 |+     set_element(
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:13:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723395572  

`set_element` should receive a pointer to keep things consistent

> Username: ashtum  
> Created_at: 2024-08-20 16:03:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723576063  

`set_element`, `insert_element`, and `delete_element` receive references because they don't handle `nullptr`. I believe changing `new_element` to return pointers isn't a good idea either because it never returns `nullptr`. I think changing the name of the other `new_element` overload would be a better option, something like `try_create_new_element` or `try_new_element`.

> Username: vinniefalco  
> Created_at: 2024-08-20 20:50:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723962453  

`*_element` are private functions so we can do whatever we want. The rules are relaxed. We should do what is easiest for maintenance. My opinion, pointers are better than having new names and different return types. But it is up to you.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:14:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248143503  

📁 include/boost/beast/http/impl/fields.hpp

```diff
 950 |+ new_element(
 951 |+     field name,
 952 |+     string_view sname,
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:14:40 UTC  
> Updated_at: 2024-08-20 14:14:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723397424  

`field name` was a mistake on my part. It should have been `field id`. And then `sname` could just be `name` and we wouldn't have two confusingly similar variables.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-20 14:15:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248146464  

📁 include/boost/beast/http/impl/fields.hpp

```diff
 963 |+         BOOST_BEAST_ASSIGN_EC(ec, error::header_field_value_too_large);
 964 |+         return nullptr;
 965 |+     }
```

> Username: vinniefalco  
> Created_at: 2024-08-20 14:15:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723399116  

Do we also need a limit on the sum of `sname.size()` and `value.size()` (maybe plus delimiters)?

> Username: ashtum  
> Created_at: 2024-08-20 14:25:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723415516  

technically no, because we use an `unit16_t` for storing the size of value. and another `unit16_t` for its offset. so, name and value can have a size of `std::numeric_limits<std::uint16_t>::max() - 2`.


---

## Review 12 [Commented]

> Username: ashtum  
> Created_at: 2024-08-20 19:53:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2248972890  

📁 include/boost/beast/http/impl/basic_parser.ipp

```diff
 118 |         BOOST_ASSERT(! is_done());
 119 |         n = static_cast<std::size_t>(p1 - p);
 120 |         if(p >= p1)
```

> Username: ashtum  
> Created_at: 2024-08-20 19:53:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723904328  

@vinniefalco I couldn't find any scenario that makes `p > p1` possible. Do you remember the rationale behind this line? If the only possibility is `p == p1`, we can completely remove this branch and allow `parse_fields` to be called with `n = 0`

> Username: vinniefalco  
> Created_at: 2024-08-20 20:55:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1723967459  

When I write these types of conditions, I make them inequalities on purpose, for no particular reason.


---

## Review 13 [Commented]

> Username: ashtum  
> Created_at: 2024-08-21 14:51:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2920#pullrequestreview-2251047433  

📁 include/boost/beast/http/impl/basic_parser.ipp

```diff
 702 |-     else
 603 |+ 
 604 |+     auto const eol = find_eol(p + skip_, pend, ec);
```

> Username: ashtum  
> Created_at: 2024-08-21 14:51:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1725200460  

@vinniefalco Now that we've removed `maybe_need_more`, I'm wondering if it's still worth keeping `skip_`. It's only used in this one place.

> Username: vinniefalco  
> Created_at: 2024-08-21 17:22:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1725456979  

hard for me to say. you will need to decide that

> Username: ashtum  
> Created_at: 2024-08-21 18:15:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2920#discussion_r1725519793  

It's pessimization when the chunk header can be read in one go, which is indeed the case most of the time because the chunk header is much smaller than the read buffer. I'd prefer to remove it and simplify the code flow and state.


---
