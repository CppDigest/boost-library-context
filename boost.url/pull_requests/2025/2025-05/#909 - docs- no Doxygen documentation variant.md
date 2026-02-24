# #909 docs: no Doxygen documentation variant [Merged]

> Username: alandefreitas  
> Created at: 2025-05-19 19:14:41 UTC  
> Updated at: 2025-05-19 21:28:34 UTC  
> Merged at: 2025-05-19 21:28:34 UTC  
> Closed at: 2025-05-19 21:28:34 UTC  
> Url: https://github.com/boostorg/url/pull/909  

This commit removes comments in the Doxygen documentation format from source files. This documentation format was there in a transition stage where we supported both Doxygen and MrDocs.  
  
We only leave documentation in the MrDocs format now. This means the `BOOST_URL_DOCS` macro is no longer necessary: symbols are documented where they are defined instead of fake implementation defined symbols.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-05-19 19:21:27 UTC  
> Url: https://github.com/boostorg/url/pull/909#issuecomment-2892044071  

An automated preview of the documentation is available at [https://909.url.prtest2.cppalliance.org/index.html](https://909.url.prtest2.cppalliance.org/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-05-19 19:30:25 UTC  
> Url: https://github.com/boostorg/url/pull/909#issuecomment-2892062571  

GCOVR code coverage report [https://909.url.prtest2.cppalliance.org/gcovr/index.html](https://909.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://909.url.prtest2.cppalliance.org/genhtml/index.html](https://909.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://909.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://909.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
