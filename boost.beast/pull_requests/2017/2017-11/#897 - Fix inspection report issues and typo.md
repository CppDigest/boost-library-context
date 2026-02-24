# #897 Fix inspection report issues and typo [Closed]

> Username: zerotypos-found  
> Created at: 2017-11-21 03:10:13 UTC  
> Updated at: 2017-11-23 04:05:16 UTC  
> Closed at: 2017-11-22 19:51:46 UTC  
> Url: https://github.com/boostorg/beast/pull/897  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-21 04:22:48 UTC  
> Url: https://github.com/boostorg/beast/pull/897#issuecomment-345912792  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=h1) Report  
> Merging [#897](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6e6e1f6fbc93b92c8b21d673933e3b77951b4fa0?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/897/graphs/tree.svg?width=650&height=150&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #897   +/-   ##  
========================================  
  Coverage    95.69%   95.69%             
========================================  
  Files          104      104             
  Lines        10454    10454             
========================================  
  Hits         10004    10004             
  Misses         450      450  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/accept.ipp](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.ipp](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=footer). Last update [6e6e1f6...0000f52](https://codecov.io/gh/boostorg/beast/pull/897?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-11-22 19:51:44 UTC  
> Url: https://github.com/boostorg/beast/pull/897#issuecomment-346456551  

Part of #898

---

## Comment 3

> Username: zerotypos-found  
> Created_at: 2017-11-23 04:05:16 UTC  
> Url: https://github.com/boostorg/beast/pull/897#issuecomment-346528145  

FYI, you can see the inspection report by  
1. Go to http://www.boost.org/  
2. Click "Development" at the right menu bar  
3. Click "Testing" at the right menu bar  
4. Then, there is "Inspection Reports".  
  
However, [the report](http://boost.cowic.de/rc/docs-inspect-develop.html) is not updated frequently and so this report does not include the inspection results of Boost.Beast yet.  
  
You can also **make the report by yourself** by  
1. Run `b2` at `BOOST_ROOT/tools/inspect`.  
2. Then, the `inspect` tool is built (the tool is located somewhere).  
3. Run `inspect` and pass it the path to boost root's directory as a command-line argument (e.g. `inspect "$BOOST_ROOT" > inspect.html`).  
  
(This PR fixed only a fraction of inspection report issues.)

---
