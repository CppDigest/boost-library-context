# #786 docs: convert qbk content to asciidoc [Merged]

> Username: alandefreitas  
> Created at: 2023-11-07 21:34:08 UTC  
> Updated at: 2023-11-08 15:30:53 UTC  
> Merged at: 2023-11-08 15:30:52 UTC  
> Closed at: 2023-11-08 15:30:53 UTC  
> Url: https://github.com/boostorg/url/pull/786  

This PR transcribes the exposition from qbk to asciidoc. It includes all the content from the exposition. The content that requires special macros and extensions (such as reference links, macros, and snippets) is transcribed literally for now. More complex extensions and the reference will be included in later PRs.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-11-07 21:42:50 UTC  
> Url: https://github.com/boostorg/url/pull/786#issuecomment-1800220058  

An automated preview of the documentation is available at [https://786.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://786.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-11-07 21:43:48 UTC  
> Url: https://github.com/boostorg/url/pull/786#issuecomment-1800223622  

Antora version: an automated preview of the documentation is available at [https://786.urlantora.prtest2.cppalliance.org/site/index.html](https://786.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-11-07 21:54:00 UTC  
> Url: https://github.com/boostorg/url/pull/786#issuecomment-1800242061  

GCOVR code coverage report [https://786.url.prtest2.cppalliance.org/gcovr/index.html](https://786.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://786.url.prtest2.cppalliance.org/genhtml/index.html](https://786.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://786.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://786.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: sdarwin  
> Created_at: 2023-11-07 21:55:02 UTC  
> Url: https://github.com/boostorg/url/pull/786#issuecomment-1800243463  

Earlier today, the doc builds were working.  
  
With this pull request 786 both GHA and Jenkins have warnings or errors.       
  
Might be upstream network problems, or code changes in this commit.  An `npm` command was commented out.  
  
- The following package was not found and will be installed: antora@3.1.4  
  
-  gulp-vinyl-zip@2.5.0: Package no longer supported. Contact Support at https://www.npmjs.com/support for more info.  
  
- No files were found with the provided path: doc/build. No artifacts will be uploaded.

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2023-11-07 21:57:01 UTC  
> Updated_at: 2023-11-07 21:57:52 UTC  
> Url: https://github.com/boostorg/url/pull/786#issuecomment-1800246719  

> An npm command was commented out.  
  
Fixed  
  
> the doc builds were working  
  
I didn't know that was already working. Nice :)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-11-07 22:07:47 UTC  
> Url: https://github.com/boostorg/url/pull/786#issuecomment-1800265459  

An automated preview of the documentation is available at [https://786.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://786.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-11-07 22:08:47 UTC  
> Url: https://github.com/boostorg/url/pull/786#issuecomment-1800267516  

Antora version: an automated preview of the documentation is available at [https://786.urlantora.prtest2.cppalliance.org/site/index.html](https://786.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2023-11-07 22:17:38 UTC  
> Updated_at: 2023-11-07 22:20:17 UTC  
> Url: https://github.com/boostorg/url/pull/786#issuecomment-1800279206  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/786?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#786](https://app.codecov.io/gh/boostorg/url/pull/786?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (771df61) into [develop](https://app.codecov.io/gh/boostorg/url/commit/e9f69ef7f00b40a3dbc69ce5fe39cb7d61f68a51?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e9f69ef) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/786/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/786?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #786   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/786?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/786?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e9f69ef...771df61](https://app.codecov.io/gh/boostorg/url/pull/786?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-11-07 22:22:06 UTC  
> Url: https://github.com/boostorg/url/pull/786#issuecomment-1800284793  

GCOVR code coverage report [https://786.url.prtest2.cppalliance.org/gcovr/index.html](https://786.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://786.url.prtest2.cppalliance.org/genhtml/index.html](https://786.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://786.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://786.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
