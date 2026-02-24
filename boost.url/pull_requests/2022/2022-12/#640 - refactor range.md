# #640 refactor range [Closed]

> Username: vinniefalco  
> Created at: 2022-12-20 01:29:16 UTC  
> Updated at: 2022-12-21 19:21:49 UTC  
> Closed at: 2022-12-21 19:21:49 UTC  
> Url: https://github.com/boostorg/url/pull/640  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-12-20 01:42:30 UTC  
> Url: https://github.com/boostorg/url/pull/640#issuecomment-1358719641  

GCOVR code coverage report [https://640.url.prtest.cppalliance.org/gcovr/index.html](https://640.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://640.url.prtest.cppalliance.org/genhtml/index.html](https://640.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://640.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://640.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-12-20 01:50:57 UTC  
> Url: https://github.com/boostorg/url/pull/640#issuecomment-1358724255  

GCOVR code coverage report [https://640.url.prtest.cppalliance.org/gcovr/index.html](https://640.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://640.url.prtest.cppalliance.org/genhtml/index.html](https://640.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://640.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://640.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Review 3 [Commented]

> Username: alandefreitas  
> Created_at: 2022-12-20 17:25:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/640#pullrequestreview-1224820042  

OK. I think I get it now.   
  
The API remains (mostly?) the same but the new strategy for type-erasing is always referring to a static variable whenever the rules are empty instead of regular type-erasure which eliminates the buffer (pro) at the cost of allocations for stateful rules (con), right?   
  
I have no precise idea of important these stateful rules are in our own rules. In a quick search, I found a `grammar::delim_rule` in all instances of `range_rule`. Doesn't that mean the rules in `range_rule` will never be empty? Maybe we can fix this with a `grammar::delim_rule` whose delimiter is a template though (some form of `delim_rule<'/'>`) but I'm not sure this is valid and fixes it.

📁 include/boost/url/grammar/detail/any_iter_rule.hpp

```diff
  23 |+ template<class T>
  24 |+ struct BOOST_SYMBOL_VISIBLE
  25 |+     any_iter_rule
```

> Username: alandefreitas  
> Created_at: 2022-12-20 17:07:30 UTC  
> Updated_at: 2022-12-20 17:25:40 UTC  
> Url: https://github.com/boostorg/url/pull/640#discussion_r1053550921  

To be honest I usually miss some small comments to avoid spending time reverse-engineering other classes to completely understand this one.


📁 include/boost/url/grammar/optional_rule.hpp

```diff
  64 | template<class Rule>
  65 | struct optional_rule_t
  66 |+     : private urls::detail::empty_value<Rule>
```

> Username: alandefreitas  
> Created_at: 2022-12-20 17:19:39 UTC  
> Updated_at: 2022-12-20 17:25:40 UTC  
> Url: https://github.com/boostorg/url/pull/640#discussion_r1053562264  

Nice :)


📁 test/unit/grammar/range_rule.cpp

```diff
  99 |         }
 100 | 
 101 |-         // range(range&&)
```

> Username: alandefreitas  
> Created_at: 2022-12-20 17:21:52 UTC  
> Updated_at: 2022-12-20 17:25:40 UTC  
> Url: https://github.com/boostorg/url/pull/640#discussion_r1053564094  

These removed tests broke?

> Username: vinniefalco  
> Created_at: 2022-12-20 21:13:23 UTC  
> Url: https://github.com/boostorg/url/pull/640#discussion_r1053754230  

I figure there's no need for move members since copies are cheap.

---

📁 test/unit/grammar/range_rule.cpp

```diff
  76 |     testRange()
  77 |     {
  78 |-         constexpr auto r0 = range_rule(
```

> Username: alandefreitas  
> Created_at: 2022-12-20 17:22:43 UTC  
> Updated_at: 2022-12-20 17:25:40 UTC  
> Url: https://github.com/boostorg/url/pull/640#discussion_r1053564808  

Do we have a plan for `constexpr` rules that are inline in headers that depend on ranges? Or we don't need a plan?


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-12-20 22:50:14 UTC  
> Url: https://github.com/boostorg/url/pull/640#issuecomment-1360414132  

GCOVR code coverage report [https://640.url.prtest.cppalliance.org/gcovr/index.html](https://640.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://640.url.prtest.cppalliance.org/genhtml/index.html](https://640.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://640.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://640.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-12-21 19:21:45 UTC  
> Url: https://github.com/boostorg/url/pull/640#issuecomment-1361982858  

Obsoleted by https://github.com/boostorg/url/pull/642

---
