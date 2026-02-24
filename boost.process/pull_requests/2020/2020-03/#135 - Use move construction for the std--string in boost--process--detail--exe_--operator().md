# #135 Use move construction for the std::string in boost::process::detail::exe_::operator() [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 05:58:23 UTC  
> Updated at: 2020-05-20 17:04:45 UTC  
> Closed at: 2020-05-20 13:51:11 UTC  
> Url: https://github.com/boostorg/process/pull/135  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-13 06:30:07 UTC  
> Updated_at: 2020-03-13 06:41:09 UTC  
> Url: https://github.com/boostorg/process/pull/135#issuecomment-598572282  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/135?src=pr&el=h1) Report  
> Merging [#135](https://codecov.io/gh/boostorg/process/pull/135?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/668579ed6f7e3ed8fa907b31c2ca2b091df5b8e8?src=pr&el=desc) will **decrease** coverage by `6.65%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/135/graphs/tree.svg?width=650&token=AhunMqTSpA&height=150&src=pr)](https://codecov.io/gh/boostorg/process/pull/135?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #135      +/-   ##  
===========================================  
- Coverage    89.14%   82.48%   -6.66%       
===========================================  
  Files          110      111       +1       
  Lines         3132     3009     -123       
===========================================  
- Hits          2792     2482     -310       
- Misses         340      527     +187  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/process/pull/135?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/process/exe.hpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4ZS5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/process/shell.hpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL3NoZWxsLmhwcA==) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/detail/posix/shell\_path.hpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9zaGVsbF9wYXRoLmhwcA==) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/exception.hpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2V4Y2VwdGlvbi5ocHA=) | `0% <0%> (-100%)` | :arrow_down: |  
| [...nclude/boost/process/detail/posix/group\_handle.hpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9wb3NpeC9ncm91cF9oYW5kbGUuaHBw) | `0% <0%> (-100%)` | :arrow_down: |  
| [include/boost/process/group.hpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2dyb3VwLmhwcA==) | `15.38% <0%> (-78.74%)` | :arrow_down: |  
| [include/boost/process/detail/config.hpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2RldGFpbC9jb25maWcuaHBw) | `0% <0%> (-61.54%)` | :arrow_down: |  
| [include/boost/process/error.hpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9jZXNzL2Vycm9yLmhwcA==) | `60% <0%> (-40%)` | :arrow_down: |  
| [test/run\_exe\_path.cpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-dGVzdC9ydW5fZXhlX3BhdGguY3Bw) | `60% <0%> (-40%)` | :arrow_down: |  
| [test/search\_path.cpp](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree#diff-dGVzdC9zZWFyY2hfcGF0aC5jcHA=) | `63.63% <0%> (-36.37%)` | :arrow_down: |  
| ... and [79 more](https://codecov.io/gh/boostorg/process/pull/135/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/135?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/135?src=pr&el=footer). Last update [668579e...3572a5b](https://codecov.io/gh/boostorg/process/pull/135?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 13:51:11 UTC  
> Url: https://github.com/boostorg/process/pull/135#issuecomment-631486575  

Template is for forward-declaration & your changes don't avoid any copy.

---

## Comment 3

> Username: jonesmz  
> Created_at: 2020-05-20 16:33:25 UTC  
> Url: https://github.com/boostorg/process/pull/135#issuecomment-631586852  

Your statement about the template being for forward declaration does not make any sense. Please elaborate.  
  
  
This change. By itself. Does not avoid copying. But it does enable other code to avoid copying by using std::move when calling the functions modified in this commit.  
  
The trade off for this change is either copy sizeof(void*) and then dereference, or copy 2*sizeof(void*) without a dereference.   
  
However the status quo makes it impossible for users of these functions to std::move() into these functions, whereas this commit enables that as a future improvement.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 17:04:45 UTC  
> Url: https://github.com/boostorg/process/pull/135#issuecomment-631604172  

If `boost::filesystem::path` is a forward declaration and the function are not templated, the compiler will try to compile them. if it's a template with a default argument the compiler will be compile them on usage, at which point `filesystem::path` would be available.  
  
Adding lvalue reference overloads would be the right way to do that, or rather just `string_view`'s if I ever chose to make a breaking change.

---
