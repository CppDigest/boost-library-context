# #69 Adding Code coverage to ublas [Merged]

> Username: coder3101  
> Created at: 2019-05-18 16:49:49 UTC  
> Updated at: 2019-05-19 12:53:19 UTC  
> Merged at: 2019-05-19 12:35:01 UTC  
> Closed at: 2019-05-19 12:35:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/69  

This PR brings the code-coverage support on codecov for ublas.  
- Currently, all coverage reports are gathered for every unit test executed. If we want to only limit the coverage reporting to some modules (say tensor). Please remove the file`--coverage` linker and compiler flag of gcc from Jamfile of respective tests.   
- PR also has some minor formatting here in tensor.hpp file. (No big change).  
- It also adds .gitignore file for excluding some IDE related folders (vscode in this case)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-05-18 17:20:16 UTC  
> Updated_at: 2019-05-18 20:28:33 UTC  
> Url: https://github.com/boostorg/ublas/pull/69#issuecomment-493692898  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/69?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@782ce16`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/69/graphs/tree.svg?width=650&token=CShoBArRWq&height=150&src=pr)](https://codecov.io/gh/boostorg/ublas/pull/69?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #69   +/-   ##  
==========================================  
  Coverage           ?   65.09%             
==========================================  
  Files              ?       61             
  Lines              ?     9233             
  Branches           ?     3000             
==========================================  
  Hits               ?     6010             
  Misses             ?      894             
  Partials           ?     2329  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ublas/pull/69?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/numeric/ublas/tensor/tensor.hpp](https://codecov.io/gh/boostorg/ublas/pull/69/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci90ZW5zb3IuaHBw) | `75.2% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/69?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/69?src=pr&el=footer). Last update [782ce16...1f5105a](https://codecov.io/gh/boostorg/ublas/pull/69?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: bassoy  
> Created_at: 2019-05-18 18:10:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/69#pullrequestreview-239207708  

📁 .appveyor.yml

```diff
  12 |     - develop
  13 |     - /feature\/.*/
  14 |+     - expr_template
```

> Username: bassoy  
> Created_at: 2019-05-18 18:10:58 UTC  
> Updated_at: 2019-05-18 18:57:00 UTC  
> Url: https://github.com/boostorg/ublas/pull/69#discussion_r285351289  

This should not be merged into the develop branch


---

## Review 3 [Commented]

> Username: bassoy  
> Created_at: 2019-05-18 18:11:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/69#pullrequestreview-239207714  

📁 .travis.yml

```diff
  14 |     - doc
  15 |     - ci
  16 |+     - expr_template
```

> Username: bassoy  
> Created_at: 2019-05-18 18:11:14 UTC  
> Updated_at: 2019-05-18 18:57:00 UTC  
> Url: https://github.com/boostorg/ublas/pull/69#discussion_r285351294  

This should not be merged into the develop branch

> Username: coder3101  
> Created_at: 2019-05-18 18:23:35 UTC  
> Updated_at: 2019-05-18 18:57:00 UTC  
> Url: https://github.com/boostorg/ublas/pull/69#discussion_r285351707  

This is outdated. In the last commit named I have removed this. Please check the final changes

> Username: bassoy  
> Created_at: 2019-05-18 18:25:14 UTC  
> Updated_at: 2019-05-18 18:57:00 UTC  
> Url: https://github.com/boostorg/ublas/pull/69#discussion_r285351771  

yes. I now have seen it.


---

## Comment 4

> Username: coder3101  
> Created_at: 2019-05-18 18:26:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/69#issuecomment-493697310  

Those are outdated commits that were previously there. Last commit b27cad3 fixes this already.

---

## Comment 5

> Username: coder3101  
> Created_at: 2019-05-18 19:03:21 UTC  
> Url: https://github.com/boostorg/ublas/pull/69#issuecomment-493699743  

I had to to a force-push because of squashing the commits into one commit.

---
