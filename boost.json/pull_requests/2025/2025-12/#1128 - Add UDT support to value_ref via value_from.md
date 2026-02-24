# #1128 Add UDT support to value_ref via value_from [Closed]

> Username: Copilot  
> Created at: 2025-12-01 17:46:04 UTC  
> Updated at: 2025-12-03 21:46:27 UTC  
> Closed at: 2025-12-03 21:46:19 UTC  
> Url: https://github.com/boostorg/json/pull/1128  

Enables user-defined types (UDTs) to work in `value_ref` initializer lists by routing non-native types through `value_from` conversion.  
  
Previously, UDTs with `tag_invoke` overloads for `value_from_tag` could not be used directly in array/object initializer lists because `value_ref::from_const` and `from_rvalue` only supported types with native `value` constructors.  
  
### Changes  
  
- **`value_ref.hpp`**: Added `is_native_value_type<T>` trait identifying types with direct `value` constructors (JSON types, numerics, string-like, nullptr)  
- **`impl/value_ref.hpp`**: Tag dispatch in `from_const_impl` and `from_rvalue_impl` to use `value_from()` for non-native types  
- **`test/value_ref.cpp`**: Tests for UDTs in initializer lists, object values, and nested structures  
  
### Example  
  
```cpp  
struct Point { int x, y; };  
  
void tag_invoke(value_from_tag, value& jv, Point const& p) {  
    jv = { {"x", p.x}, {"y", p.y} };  
}  
  
// Now works:  
Point p{1, 2};  
array arr{p, 123};           // UDT in initializer  
object obj{{"point", p}};    // UDT as object value  
value jv = {{"data", {p}}};  // Nested UDTs  
```  
  
> [!WARNING]  
>  
> <details>  
> <summary>Firewall rules blocked me from connecting to one or more addresses (expand for details)</summary>  
>  
> #### I tried to connect to the following addresses, but was blocked by firewall rules:  
>  
> - `esm.ubuntu.com`  
>   - Triggering command: `/usr/lib/apt/methods/https /usr/lib/apt/methods/https` (dns block)  
>  
> If you need me to access, download, or install something from one of these locations, you can either:  
>  
> - Configure [Actions setup steps](https://gh.io/copilot/actions-setup-steps) to set up my environment, which run before the firewall is enabled  
> - Add the appropriate URLs or hosts to the custom allowlist in this repository's [Copilot coding agent settings](https://github.com/boostorg/json/settings/copilot/coding_agent) (admins only)  
>  
> </details>  
  
<!-- START COPILOT CODING AGENT SUFFIX -->  
  
  
  
<details>  
  
<summary>Original prompt</summary>  
  
>   
> ----  
>   
> *This section details on the original issue you should resolve*  
>   
> <issue_title>Streamlined construction and assignment, and from UDTs</issue_title>  
> <issue_description>I think we should explore getting rid of most of the constructors and assignment operators in json::value and replace them with this  
> ```  
> template< class T >  
> value( T&& t );  
>   
> template< class T >  
> value& operator=( T&& t );  
> ```  
>   
> and in the same exploration, allow `value_ref` to type erase UDTs. This will allow `jv( t.a )` and `jv = t.a` and `jv = vec` to work.  
>   
> The way to do this is to add another constrained constructor to `value_ref` which accepts UDTs when a suitable `tag_invoke` exists, and then type erase it by storing a function pointer which, when invoked, calls `tag_invoke` with the storage_ptr and produces the `json::value`.  
>   
>   
> </issue_description>  
>   
> ## Comments on the Issue (you are @copilot in this section)  
>   
> <comments>  
> <comment_new><author>@vinniefalco</author><body>  
> @claude </body></comment_new>  
> </comments>  
>   
  
  
</details>  
  
- Fixes boostorg/json#1122  
  
<!-- START COPILOT CODING AGENT TIPS -->  
---  
  
💬 We'd love your input! Share your thoughts on Copilot coding agent in our [2 minute survey](https://gh.io/copilot-coding-agent-survey).

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-12-01 21:27:22 UTC  
> Updated_at: 2025-12-03 21:46:27 UTC  
> Url: https://github.com/boostorg/json/pull/1128#issuecomment-3598956663  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1128?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.72%. Comparing base ([`64db9b7`](https://app.codecov.io/gh/boostorg/json/commit/64db9b700d5030f39c4f0cc466c14745c48b990c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`98ba3c6`](https://app.codecov.io/gh/boostorg/json/commit/98ba3c662e5c4a414c7ad131ae86420939f4e8fb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1128/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1128      +/-   ##  
===========================================  
+ Coverage    93.71%   93.72%   +0.01%       
===========================================  
  Files           91       91                
  Lines         9153     9168      +15       
===========================================  
+ Hits          8578     8593      +15       
  Misses         575      575                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1128?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/value\_ref.hpp](https://app.codecov.io/gh/boostorg/json/pull/1128?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fvalue_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWVfcmVmLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_ref.hpp](https://app.codecov.io/gh/boostorg/json/pull/1128?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fvalue_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3JlZi5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1128?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1128?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [64db9b7...98ba3c6](https://app.codecov.io/gh/boostorg/json/pull/1128?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
