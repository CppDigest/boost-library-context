# #849 docs: cpp.js uses fast-xml-parser [Merged]

> Username: alandefreitas  
> Created at: 2024-07-15 19:21:11 UTC  
> Updated at: 2024-07-23 22:23:31 UTC  
> Merged at: 2024-07-23 22:16:26 UTC  
> Closed at: 2024-07-23 22:16:26 UTC  
> Url: https://github.com/boostorg/url/pull/849  

This PR replaces xmldom with fast-xml-parser and adds support for more kinds of symbols.  
  
xmldom hasn't been receiving maintenance and has known security vulnerabilities: https://github.com/boostorg/url/security/dependabot/3  
  
The code was also refactored to support multiple ways to compose symbols (fundamental types, namespaces, search in namespaces in the tag file, template parameters, filenames, and nested classes). For instance,  
  
```adoc  
cpp:std[]  
  
cpp:bool[]  
  
cpp:std::string_view[]  
  
cpp:std::string_view[]  
  
cpp:std::FILE[]  
  
cpp:std::vector[]  
  
cpp:std::vector<int>[]  
  
cpp:std::vector<std::vector<int>,std::allocator<int>>[]  
  
cpp:MyClass<int,std::vector<double>,std::map<int,std::string>>[]  
  
cpp:std::vector<std::vector<int>,std::allocator<int>>::const_iterator[]  
  
cpp:"algorithm"[]  
  
cpp:<algorithm>[]  
  
cpp:std::abort[]  
  
cpp:std::allocator::address[]  
```  
  
generates  
  
![image](https://github.com/user-attachments/assets/ba019abc-b714-41ee-8324-461eba52ebb3)  
  
In the following PRs, the cpp.js will become an antora extension so it can accept parameters with custom tag files and base URLs.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-07-15 19:27:23 UTC  
> Url: https://github.com/boostorg/url/pull/849#issuecomment-2229228299  

Antora version: an automated preview of the documentation is available at [https://849.urlantora.prtest2.cppalliance.org/site/index.html](https://849.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-07-15 19:35:46 UTC  
> Url: https://github.com/boostorg/url/pull/849#issuecomment-2229241383  

An automated preview of the documentation is available at [https://849.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://849.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-07-15 19:47:58 UTC  
> Url: https://github.com/boostorg/url/pull/849#issuecomment-2229260168  

GCOVR code coverage report [https://849.url.prtest2.cppalliance.org/gcovr/index.html](https://849.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://849.url.prtest2.cppalliance.org/genhtml/index.html](https://849.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://849.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://849.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-07-15 19:49:54 UTC  
> Updated_at: 2024-07-16 01:48:50 UTC  
> Url: https://github.com/boostorg/url/pull/849#issuecomment-2229263034  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/849?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.21%. Comparing base [(`8398453`)](https://app.codecov.io/gh/boostorg/url/commit/83984532ab9eac8c372748b2d542243ba91a6ade?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3f8151b`)](https://app.codecov.io/gh/boostorg/url/commit/3f8151bacb1e1a98b4dd464164ebe0644efbcf72?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/849/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/849?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #849   +/-   ##  
========================================  
  Coverage    99.21%   99.21%             
========================================  
  Files          157      157             
  Lines         8422     8422             
========================================  
  Hits          8356     8356             
  Misses          66       66             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/849?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/849?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8398453...3f8151b](https://app.codecov.io/gh/boostorg/url/pull/849?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-07-23 22:23:30 UTC  
> Url: https://github.com/boostorg/url/pull/849#issuecomment-2246411006  

An automated preview of the documentation is available at [https://849.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://849.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---
