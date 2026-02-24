# #50 Specified default value for the template argument of parse_config_file [Merged]

> Username: emreturkay  
> Created at: 2018-03-22 14:52:36 UTC  
> Updated at: 2018-07-06 07:39:29 UTC  
> Merged at: 2018-07-06 07:38:44 UTC  
> Closed at: 2018-07-06 07:38:44 UTC  
> Url: https://github.com/boostorg/program_options/pull/50  



---

## Comment 1

> Username: vprus  
> Created_at: 2018-03-22 15:27:54 UTC  
> Url: https://github.com/boostorg/program_options/pull/50#issuecomment-375347862  

Thanks for the PR! For my education, what problem does this solve?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-03-25 12:06:19 UTC  
> Url: https://github.com/boostorg/program_options/pull/50#issuecomment-375965427  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/50?src=pr&el=h1) Report  
> Merging [#50](https://codecov.io/gh/boostorg/program_options/pull/50?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/program_options/commit/491cb17e3abd7f006ac343eb7789fcd8e141751a?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/50/graphs/tree.svg?height=150&src=pr&width=650&token=UMdM0EgHK3)](https://codecov.io/gh/boostorg/program_options/pull/50?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #50   +/-   ##  
========================================  
  Coverage    50.29%   50.29%             
========================================  
  Files           23       23             
  Lines         1372     1372             
  Branches       694      694             
========================================  
  Hits           690      690             
  Misses         110      110             
  Partials       572      572  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/50?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [src/parsers.cpp](https://codecov.io/gh/boostorg/program_options/pull/50/diff?src=pr&el=tree#diff-c3JjL3BhcnNlcnMuY3Bw) | `45.61% <ø> (ø)` | :arrow_up: |  
| [include/boost/program\_options/parsers.hpp](https://codecov.io/gh/boostorg/program_options/pull/50/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvcGFyc2Vycy5ocHA=) | `40% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/50?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/50?src=pr&el=footer). Last update [491cb17...9082a15](https://codecov.io/gh/boostorg/program_options/pull/50?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: emreturkay  
> Created_at: 2018-03-25 12:29:39 UTC  
> Url: https://github.com/boostorg/program_options/pull/50#issuecomment-375966773  

The change defines a default value for the "class charT" template argument of parse_config_file function for compilers supporting C++ 11 "default template arguments for function templates" feature (BOOST_NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS).  
  
This change allows one to write  
  
	parse_config_file("example.cfg", desc)  
  
instead of  
  
	parse_config_file<char>("example.cfg", desc)  
  
As a bonus, the code example in the "Storage Component" of the boost program options documentation (http://www.boost.org/doc/libs/1_66_0/doc/html/program_options/overview.html) will be valid with this change.  
  
	variables_map vm;  
	store(parse_command_line(argc, argv, desc), vm);  
	store(parse_config_file("example.cfg", desc), vm);  
	notify(vm);

---

## Comment 4

> Username: vprus  
> Created_at: 2018-07-06 07:39:29 UTC  
> Url: https://github.com/boostorg/program_options/pull/50#issuecomment-402953642  

Thanks, this is now merged to develop.

---
