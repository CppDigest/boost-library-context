# #492 Version 58 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-15 03:43:42 UTC  
> Updated at: 2017-06-16 16:21:34 UTC  
> Merged at: 2017-06-15 19:49:20 UTC  
> Closed at: 2017-06-15 19:49:20 UTC  
> Url: https://github.com/boostorg/beast/pull/492  

* Fix unaligned reads in utf8-checker  
* Qualify size_t in message template  
* Reorganize examples  
* Specification for http read  
* Avoid `std::string` in websocket  
* Fix basic_fields insert ordering  
* basic_fields::set optimization  
* basic_parser::put doc  
* Use static string in basic_fields::reader  
* Remove redundant code  
* Fix parsing chunk size with leading zeroes  
  
API Changes:  
  
* `basic_fields::set` renamed from `basic_fields::replace`  
  
Actions Required:  
  
* Rename calls to `basic_fields::replace` to `basic_fields::set`  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v58

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-15 07:48:54 UTC  
> Url: https://github.com/boostorg/beast/pull/492#issuecomment-308656087  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=h1) Report  
> Merging [#492](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/8ba182cb2e0d073724be170cb64cd3d9226f161f?src=pr&el=desc) will **decrease** coverage by `0.04%`.  
> The diff coverage is `81.81%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/492/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #492      +/-   ##  
==========================================  
- Coverage   93.76%   93.72%   -0.05%       
==========================================  
  Files          96       96                
  Lines        7045     7059      +14       
==========================================  
+ Hits         6606     6616      +10       
- Misses        439      443       +4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/detail/utf8\_checker.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3V0ZjhfY2hlY2tlci5ocHA=) | `95.91% <81.81%> (-4.09%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=footer). Last update [8ba182c...a20e39c](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-06-15 07:48:54 UTC  
> Updated_at: 2017-06-15 20:44:34 UTC  
> Url: https://github.com/boostorg/beast/pull/492#issuecomment-308656088  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=h1) Report  
> Merging [#492](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/8ba182cb2e0d073724be170cb64cd3d9226f161f?src=pr&el=desc) will **decrease** coverage by `0.25%`.  
> The diff coverage is `73.5%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/492/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #492      +/-   ##  
==========================================  
- Coverage   93.76%   93.51%   -0.26%       
==========================================  
  Files          96       96                
  Lines        7045     7093      +48       
==========================================  
+ Hits         6606     6633      +27       
- Misses        439      460      +21  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `92.02% <ø> (-0.09%)` | :arrow_down: |  
| [include/beast/http/detail/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `98.11% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/message.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `89.47% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `90.82% <69.13%> (-6.26%)` | :arrow_down: |  
| [include/beast/websocket/detail/pmd\_extension.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BtZF9leHRlbnNpb24uaHBw) | `83.89% <77.77%> (+1.95%)` | :arrow_up: |  
| [include/beast/websocket/detail/utf8\_checker.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3V0ZjhfY2hlY2tlci5ocHA=) | `95.91% <81.81%> (-4.09%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=footer). Last update [8ba182c...3f8097b](https://codecov.io/gh/vinniefalco/Beast/pull/492?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
