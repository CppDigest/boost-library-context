# #51 Reproduce description error on default template typedef [Closed]

> Username: madmongo1  
> Created at: 2020-10-23 10:12:04 UTC  
> Updated at: 2022-01-09 21:58:32 UTC  
> Closed at: 2022-01-09 21:58:32 UTC  
> Url: https://github.com/boostorg/docca/pull/51  

see issue #50

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-10-23 10:37:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/docca/pull/51#pullrequestreview-515546373  

📁 example/include/docca/issue_50.hpp

```diff
  57 |+ class basic_issue_50
  58 |+ #if ! BOOST_BEAST_DOXYGEN
  59 |+ : private boost::empty_value<Allocator>
```

> Username: vinniefalco  
> Created_at: 2020-10-23 10:37:35 UTC  
> Url: https://github.com/boostorg/docca/pull/51#discussion_r510794100  

Do we really need all this? Please trim it all the way down to the smallest declaration which reproduces the defect. The less code and comments, the faster the doc toolchain runs.


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-11-03 22:14:12 UTC  
> Url: https://github.com/boostorg/docca/pull/51#issuecomment-960222771  

An automated preview of the documentation is available at [https://51.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://51.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---
