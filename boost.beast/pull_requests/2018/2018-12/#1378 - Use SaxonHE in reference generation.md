# #1378 Use SaxonHE in reference generation [Closed]

> Username: djarek  
> Created at: 2018-12-19 23:34:21 UTC  
> Updated at: 2019-09-10 21:03:30 UTC  
> Closed at: 2019-04-11 20:58:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1378  

Depends on: https://github.com/boostorg/build/pull/378

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-19 23:57:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1378#pullrequestreview-186801244  

📁 doc/docca/include/docca/doxygen.xsl

```diff
1211 |     <xsl:text>[table [[Name][Description]]&#xd;</xsl:text>
1212 |-     <xsl:for-each select="sectiondef[@kind='private-attrib' or @kind='private-static-attrib']/memberdef" mode="class-table">
1212 |+     <xsl:for-each select="sectiondef[@kind='private-attrib' or @kind='private-static-attrib']/memberdef"  >
```

> Username: vinniefalco  
> Created_at: 2018-12-19 23:57:07 UTC  
> Updated_at: 2019-02-16 18:13:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1378#discussion_r243117580  

There seems to be extra spacing before the `>` ?

> Username: djarek  
> Created_at: 2018-12-20 02:37:57 UTC  
> Updated_at: 2019-02-16 18:13:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1378#discussion_r243141252  

Removed.


---

## Comment 2

> Username: stale[bot]  
> Created_at: 2019-01-19 17:12:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1378#issuecomment-455797655  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2019-02-16 18:37:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1378#issuecomment-464370605  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1378?src=pr&el=h1) Report  
> Merging [#1378](https://codecov.io/gh/boostorg/beast/pull/1378?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/d43d9421a40c0251614bc45ea6dcf921a3dbaf37?src=pr&el=desc) will **increase** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1378/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1378?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1378      +/-   ##  
===========================================  
+ Coverage    93.92%   93.96%   +0.03%       
===========================================  
  Files          134      134                
  Lines        11661    11661                
===========================================  
+ Hits         10953    10957       +4       
+ Misses         708      704       -4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1378?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1378/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `87.1% <0%> (+0.69%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1378?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1378?src=pr&el=footer). Last update [d43d942...79af262](https://codecov.io/gh/boostorg/beast/pull/1378?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2019-02-20 23:30:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1378#issuecomment-465798076  

This should wait until after 1.70.

---

## Comment 5

> Username: stale[bot]  
> Created_at: 2019-03-23 00:12:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1378#issuecomment-475821199  

This issue has been open for a while with no activity, has it been resolved?

---
