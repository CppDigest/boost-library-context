# #739 One TU per ipp file [Merged]

> Username: alandefreitas  
> Created at: 2023-04-14 04:42:34 UTC  
> Updated at: 2024-03-29 14:59:07 UTC  
> Merged at: 2023-04-14 18:46:53 UTC  
> Closed at: 2023-04-14 18:46:53 UTC  
> Url: https://github.com/boostorg/url/pull/739  

This PR is a continuation of #737. Besides what #737 implements, it fixes all other errors making the conversion impossible. This included a number of adjustments in build scripts and CI.  
  
- Both `drone.sh` and `ci.yml` had bugs where url was being copied on top of what was cloned instead of replacing it. This works fine most of the time. But when files are removed instead of being changed, build scripts in both `b2` and `cmake` would end up globbing sources from the old and the new version.  
- Some macros to export/import symbols had to be propagated to other targets (`PUBLIC` in CMake / `common-requirements` in `b2`) while others didn't. For instance, `BOOST_URL_DYN_LINK` should be propagated while `BOOST_URL_SOURCE` shouldn't.  
- The router example had to be adapted because it was relying on the `src.hpp` method to include the library and to define its own library. Like the library itself, the example was converted to one TU per ipp file. Tests were also adapted to now include these new cpp files.  
- `src.hpp` had to go. Not only it was unused but it also caused problems with globbing.  
- The limits test had conflicts because boost_url does not propagate the properties we want for the tests while adding more properties would cause conflicts. The limits tests just need another version of the library because `BOOST_URL_SOURCE` cannot be public to it but still needs to exist privately to the library it link to. Build scripts were adapted to create a small variation of the library target where `BOOST_URL_MAX_SIZE` is changed.  
- After the transformation in the router example, we had a bug in a very specific interaction between the router example, the router unit tests, and `b2`. This interaction wasn't possible before when we only had ipp files. When the tests compile `router.cpp`, it creates the object file for it as `router.cpp.o`. When the examples compile `router.cpp`, then `b2` compiles another `router.cpp.o`, as expected, because examples and tests define different global target properties. The problem is `b2` attempts to save these object files in the same location and throws an error because the object files have different properties. This means compiling the tests or the examples individually works, but attempting to compile both fails. CMake, on the other hand, does not have this problem. Because this is a complex problem pretty much outside the scope of this PR for which I have no solution, I removed the router example from the set of targets `b2` should compile with the examples. CI is still checking the example via CMake. That should allow the library to move forward with this much more important issue while we don't have a solution for this smaller issue.   
  
A great part of the problems was hard to identify because the CI scripts are a mess and confusing. At many points, GHA tests passed and drone didn't, and vice-versa. They are large, difficult to reason about, difficult to maintain, and not composable. `ci.yml` is 517 of code to achieve very little. For instance, `BoostServerTech/**/ci.yml` achieves much more with just 67 lines of code (almost half of which are just the test matrix), including installing packages with and integrating vcpkg, apt-get, much more reliable and efficient boost caching, and the complete cmake workflow. I'll have a better look at this once we are done with this more important issue.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-04-14 05:00:27 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-1507925605  

GCOVR code coverage report [https://739.url.prtest.cppalliance.org/gcovr/index.html](https://739.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://739.url.prtest.cppalliance.org/genhtml/index.html](https://739.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://739.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://739.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-04-14 07:00:47 UTC  
> Updated_at: 2023-04-14 16:54:32 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-1508021537  

## [Codecov](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#739](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9297a7b) into [develop](https://codecov.io/gh/boostorg/url/commit/d769abedb56fd8ab10dfdbd9c98b78274ccccb00?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d769abe) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 9297a7b differs from pull request most recent head 04552c7. Consider uploading reports for the commit 04552c7 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/739/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #739   +/-   ##  
========================================  
  Coverage    97.22%   97.22%             
========================================  
  Files          155      155             
  Lines         8517     8517             
========================================  
  Hits          8281     8281             
  Misses         236      236             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/router/detail/impl/router.cpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvZGV0YWlsL2ltcGwvcm91dGVyLmNwcA==) | `100.00% <ø> (ø)` | |  
| [example/router/detail/router.hpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvZGV0YWlsL3JvdXRlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [example/router/impl/matches.cpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvaW1wbC9tYXRjaGVzLmNwcA==) | `100.00% <ø> (ø)` | |  
| [example/router/matches.hpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvbWF0Y2hlcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/normalize.hpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL25vcm1hbGl6ZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/print.hpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3ByaW50LmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/authority\_view.cpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2F1dGhvcml0eV92aWV3LmNwcA==) | `85.11% <ø> (ø)` | |  
| [src/decode\_view.cpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RlY29kZV92aWV3LmNwcA==) | `100.00% <ø> (ø)` | |  
| [src/detail/any\_params\_iter.cpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9hbnlfcGFyYW1zX2l0ZXIuY3Bw) | `99.44% <ø> (ø)` | |  
| [src/detail/any\_segments\_iter.cpp](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9hbnlfc2VnbWVudHNfaXRlci5jcHA=) | `100.00% <ø> (ø)` | |  
| ... and [61 more](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d769abe...04552c7](https://codecov.io/gh/boostorg/url/pull/739?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-04-14 17:02:09 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-1508966419  

An automated preview of the documentation is available at [https://739.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://739.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-04-14 17:11:29 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-1508977789  

GCOVR code coverage report [https://739.url.prtest.cppalliance.org/gcovr/index.html](https://739.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://739.url.prtest.cppalliance.org/genhtml/index.html](https://739.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://739.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://739.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: the-nic  
> Created_at: 2024-03-28 10:10:29 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-2024835723  

It would have been nice to document the api breakage in the changelog.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2024-03-28 17:40:18 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-2025774977  

What API breakage? There should not be any API changes here...

---

## Comment 7

> Username: the-nic  
> Created_at: 2024-03-28 17:43:01 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-2025779477  

The header-only builds had been removed without notice :(

---

## Comment 8

> Username: alandefreitas  
> Created_at: 2024-03-28 18:51:31 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-2025895944  

That's not an API breakage. It only changes the way you need to consume the library.   
  
There's never been a real "header-only" version of the library. It has always been a compiled library. The ipp files were included in a cpp source file and they had to be compiled as usual.   
  
One TU per ipp file just makes it more efficient to compile and more transparent when consuming it. It's now more like Boost.Filesystem, where you just link the component instead of asking the user to compile it manually.

---

## Comment 9

> Username: the-nic  
> Created_at: 2024-03-29 07:22:16 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-2026799313  

It was documented as such, and it broke the builds using this documented behavior, I'm not saying removing it was wrong - thats up to you.  
But it broke previously working builds and previously documented behaviour - without notice. It should have been mentioned in the release notes/changelog IMHO.

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2024-03-29 14:59:06 UTC  
> Url: https://github.com/boostorg/url/pull/739#issuecomment-2027353302  

> It should have been mentioned in the release notes/changelog IMHO.  
  
Yes you are right, this should have been mentioned. There should have been a deprecation period with a compiler warning for at least 1 release, followed by removal, with ample notes in the change log.

---
