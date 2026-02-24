# #790 docs: reference collector and cpp extension [Merged]

> Username: alandefreitas  
> Created at: 2023-11-10 22:47:39 UTC  
> Updated at: 2024-02-21 00:55:34 UTC  
> Merged at: 2023-11-18 00:14:11 UTC  
> Closed at: 2023-11-18 00:14:11 UTC  
> Url: https://github.com/boostorg/url/pull/790  

This PR implements the reference (like in PR #788) but it uses a collector extension that runs mrdocs when the main playbook is rendering the files instead of pushing hundreds of files to the repository.  
  
The PR also includes the basic implementation for the cpp Antora extension. The extension renders `cpp` inline macros with links that come from a Doxygen [tagfile](https://www.doxygen.nl/manual/external.html). This version includes a recent version of the cppreference tag file among the default tag files but we still need to agree on a convention for other tag files. Tag files only contain relative paths, so we also need to agree on a convention to determine the base URL for each tag file.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-11-10 22:58:00 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806538143  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-11-10 22:59:14 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806539306  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-11-10 23:02:19 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806541458  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-11-10 23:12:15 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806548657  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-11-10 23:12:59 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806549065  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-11-10 23:28:14 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806557503  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-11-10 23:58:03 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806575016  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-11-10 23:59:32 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806575706  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-11-11 00:09:59 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806582657  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 10

> Username: codecov[bot]  
> Created_at: 2023-11-11 00:11:03 UTC  
> Updated_at: 2023-11-17 21:54:59 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806583739  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/790?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#790](https://app.codecov.io/gh/boostorg/url/pull/790?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6f9e41d) into [develop](https://app.codecov.io/gh/boostorg/url/commit/2ecde1f5682c4a5f1082914c09782fa13b09b76c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2ecde1f) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/790/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/790?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #790   +/-   ##  
========================================  
  Coverage    97.27%   97.27%             
========================================  
  Files          156      156             
  Lines         8565     8565             
========================================  
  Hits          8332     8332             
  Misses         233      233             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/790?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/790?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2ecde1f...6f9e41d](https://app.codecov.io/gh/boostorg/url/pull/790?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2023-11-11 12:55:57 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806810159  

Wow, that was fast!

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2023-11-11 12:56:38 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1806810283  

Can we please not commit 1,300 adoc files to the repo this time?

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-11-14 00:57:53 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809373106  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-11-14 00:58:23 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809373484  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-11-14 01:12:36 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809384159  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-11-14 01:23:00 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809391699  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-11-14 01:23:29 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809392043  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-11-14 01:33:47 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809399519  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2023-11-14 01:38:14 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809402457  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2023-11-14 01:38:45 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809402780  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2023-11-14 01:41:35 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809404588  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2023-11-14 01:51:34 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809412225  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2023-11-14 01:53:16 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809413304  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2023-11-14 02:08:01 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809427536  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2023-11-14 02:08:31 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809427842  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2023-11-14 02:09:10 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809428309  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2023-11-14 02:26:45 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809442399  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2023-11-14 02:28:05 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809443334  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2023-11-14 02:28:33 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809443773  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2023-11-14 02:44:20 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1809458334  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2023-11-14 16:38:04 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1810647199  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2023-11-14 16:38:32 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1810647986  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2023-11-14 16:53:16 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1810684599  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2023-11-14 16:58:02 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1810693524  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2023-11-14 16:58:30 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1810694304  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2023-11-14 17:10:11 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1810715192  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2023-11-14 19:22:58 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811047495  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2023-11-14 19:23:27 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811048126  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2023-11-14 19:36:57 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811069800  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 40

> Username: cppalliance-bot  
> Created_at: 2023-11-14 19:37:55 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811071425  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 41

> Username: cppalliance-bot  
> Created_at: 2023-11-14 19:38:24 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811074578  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 42

> Username: cppalliance-bot  
> Created_at: 2023-11-14 19:53:31 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811120214  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 43

> Username: cppalliance-bot  
> Created_at: 2023-11-14 20:02:57 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811140666  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 44

> Username: cppalliance-bot  
> Created_at: 2023-11-14 20:03:27 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811141295  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 45

> Username: cppalliance-bot  
> Created_at: 2023-11-14 20:12:52 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811165168  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2023-11-14 20:38:04 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811231580  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2023-11-14 20:46:52 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811250685  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 48

> Username: cppalliance-bot  
> Created_at: 2023-11-14 20:53:03 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811261007  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2023-11-14 21:08:09 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811313364  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 50

> Username: cppalliance-bot  
> Created_at: 2023-11-14 21:16:51 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811323551  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 51

> Username: cppalliance-bot  
> Created_at: 2023-11-14 21:21:53 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811335800  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 52

> Username: cppalliance-bot  
> Created_at: 2023-11-14 21:38:03 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811364552  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 53

> Username: cppalliance-bot  
> Created_at: 2023-11-14 21:46:32 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811374741  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 54

> Username: cppalliance-bot  
> Created_at: 2023-11-14 21:53:09 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811383207  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 55

> Username: cppalliance-bot  
> Created_at: 2023-11-14 22:02:58 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811433830  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 56

> Username: cppalliance-bot  
> Created_at: 2023-11-14 22:11:34 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811443870  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 57

> Username: cppalliance-bot  
> Created_at: 2023-11-14 22:13:11 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811445756  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 58

> Username: cppalliance-bot  
> Created_at: 2023-11-14 22:54:35 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811521330  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 59

> Username: cppalliance-bot  
> Created_at: 2023-11-14 23:02:21 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811529391  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 60

> Username: cppalliance-bot  
> Created_at: 2023-11-14 23:07:06 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811534427  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 61

> Username: cppalliance-bot  
> Created_at: 2023-11-14 23:24:40 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811551447  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 62

> Username: cppalliance-bot  
> Created_at: 2023-11-14 23:32:26 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811558419  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 63

> Username: cppalliance-bot  
> Created_at: 2023-11-14 23:35:13 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1811560901  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 64

> Username: cppalliance-bot  
> Created_at: 2023-11-15 21:11:49 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813267495  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 65

> Username: cppalliance-bot  
> Created_at: 2023-11-15 21:22:56 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813280209  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 66

> Username: cppalliance-bot  
> Created_at: 2023-11-15 21:32:50 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813291714  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 67

> Username: cppalliance-bot  
> Created_at: 2023-11-15 21:33:43 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813292788  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 68

> Username: cppalliance-bot  
> Created_at: 2023-11-15 21:36:20 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813295990  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 69

> Username: alandefreitas  
> Created_at: 2023-11-15 21:40:27 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813303244  

@sdarwin This PR includes a lot of logic for the collector which is very easy to break because it makes assumptions about what's available in the system. I suspect the preview script is failing and the bot is just creating a comment with the previous preview. Is it possible to see what's failing? I think we will need mrdocs and clang installed for this collector script to work.

---

## Comment 70

> Username: cppalliance-bot  
> Created_at: 2023-11-15 21:51:22 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813317226  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 71

> Username: cppalliance-bot  
> Created_at: 2023-11-16 00:12:55 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813499603  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 72

> Username: cppalliance-bot  
> Created_at: 2023-11-16 00:26:17 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813511314  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 73

> Username: cppalliance-bot  
> Created_at: 2023-11-16 00:32:52 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813516553  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 74

> Username: cppalliance-bot  
> Created_at: 2023-11-16 00:41:41 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813523808  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 75

> Username: cppalliance-bot  
> Created_at: 2023-11-16 00:52:53 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813533697  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 76

> Username: cppalliance-bot  
> Created_at: 2023-11-16 00:56:40 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813536825  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 77

> Username: cppalliance-bot  
> Created_at: 2023-11-16 01:01:26 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813542690  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 78

> Username: cppalliance-bot  
> Created_at: 2023-11-16 01:27:59 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813590296  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 79

> Username: cppalliance-bot  
> Created_at: 2023-11-16 01:31:43 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813598112  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 80

> Username: cppalliance-bot  
> Created_at: 2023-11-16 01:41:47 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813617061  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 81

> Username: cppalliance-bot  
> Created_at: 2023-11-16 01:52:55 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813637847  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 82

> Username: sdarwin  
> Created_at: 2023-11-16 02:02:11 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813654670  

@alandefreitas   
  
>  I suspect the preview script  
  
The docs builds in github actions is also failing, right?  Review the logs:  
  
"Add the --stacktrace option to see the cause of the error."  
  
"No files were found with the provided path: doc/build/site. No artifacts will be uploaded."    
  
Add `set -xe` in doc/build_antora.sh (and any shell script it calls) so the errors will be more apparent.

---

## Comment 83

> Username: cppalliance-bot  
> Created_at: 2023-11-16 02:03:57 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813657733  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 84

> Username: cppalliance-bot  
> Created_at: 2023-11-16 02:12:55 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813673747  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 85

> Username: cppalliance-bot  
> Created_at: 2023-11-16 02:21:17 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813687483  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 86

> Username: alandefreitas  
> Created_at: 2023-11-16 04:59:45 UTC  
> Updated_at: 2023-11-16 05:06:21 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1813792080  

> The docs builds in github actions is also failing, right?   
  
Not really. Some runs fail and some don't as I change things but that's all under control. The ones that fail are because I'm working on a new feature but I always know why they're failing. For instance, the latest run (https://github.com/boostorg/url/actions/runs/6885338964/job/18729332722) worked fine. There's an error message in the end about a broken link but it's not fatal at all. It's functionally a warning we are aware of.  
  
This is unlike the preview, which didn't match the content of the github actions artifact. I think it fails because the collector script depends on mrdocs and a C++ compiler, then ends up creating a comment with the same content. I don't know what the error output is but, assuming the error is about it not finding these tools, I included some logic to download mrdocs directly in the script just in case it's not available. It works fine in github actions so if I'm lucky it should also work for the preview. I have the impression the content is matching now.

---

## Comment 87

> Username: cppalliance-bot  
> Created_at: 2023-11-16 20:33:02 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1815269743  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 88

> Username: cppalliance-bot  
> Created_at: 2023-11-16 20:47:59 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1815288967  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 89

> Username: cppalliance-bot  
> Created_at: 2023-11-16 20:48:24 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1815289551  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 90

> Username: cppalliance-bot  
> Created_at: 2023-11-16 21:05:49 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1815314649  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 91

> Username: cppalliance-bot  
> Created_at: 2023-11-16 22:22:56 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1815409532  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 92

> Username: cppalliance-bot  
> Created_at: 2023-11-16 22:37:12 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1815424889  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 93

> Username: cppalliance-bot  
> Created_at: 2023-11-16 22:47:57 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1815436285  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 94 [Commented]

> Username: sdarwin  
> Created_at: 2023-11-16 22:47:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/790#pullrequestreview-1735676640  

📁 doc/build_antora.sh

```diff
  23 |+ PATH="$(pwd)/node_modules/.bin:${PATH}"
  24 |+ export PATH
  25 | npx antora --clean --fetch "$PLAYBOOK"
```

> Username: sdarwin  
> Created_at: 2023-11-16 22:47:58 UTC  
> Url: https://github.com/boostorg/url/pull/790#discussion_r1396459992  

and remove "npx"     
`antora --clean --fetch "$PLAYBOOK"`


---

## Comment 95

> Username: cppalliance-bot  
> Created_at: 2023-11-16 22:53:33 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1815441737  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 96

> Username: cppalliance-bot  
> Created_at: 2023-11-16 22:57:05 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1815445203  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 97

> Username: cppalliance-bot  
> Created_at: 2023-11-17 19:17:55 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1816957625  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 98

> Username: cppalliance-bot  
> Created_at: 2023-11-17 19:32:55 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1816975654  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 99

> Username: cppalliance-bot  
> Created_at: 2023-11-17 19:34:19 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1816977305  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 100

> Username: cppalliance-bot  
> Created_at: 2023-11-17 19:47:54 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1816994382  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 101

> Username: cppalliance-bot  
> Created_at: 2023-11-17 19:52:11 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1816999208  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 102

> Username: cppalliance-bot  
> Created_at: 2023-11-17 19:53:40 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1817000925  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 103

> Username: cppalliance-bot  
> Created_at: 2023-11-17 20:09:37 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1817035354  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 104

> Username: cppalliance-bot  
> Created_at: 2023-11-17 21:02:59 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1817101825  

An automated preview of the documentation is available at [https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://790.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 105

> Username: cppalliance-bot  
> Created_at: 2023-11-17 21:08:35 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1817107916  

Antora version: an automated preview of the documentation is available at [https://790.urlantora.prtest2.cppalliance.org/site/index.html](https://790.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 106

> Username: cppalliance-bot  
> Created_at: 2023-11-17 21:15:55 UTC  
> Url: https://github.com/boostorg/url/pull/790#issuecomment-1817115946  

GCOVR code coverage report [https://790.url.prtest2.cppalliance.org/gcovr/index.html](https://790.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://790.url.prtest2.cppalliance.org/genhtml/index.html](https://790.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://790.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
