# #16 add minimum visibility support [Open]

> Username: jhunold  
> Created at: 2018-09-13 16:07:37 UTC  
> Updated at: 2025-09-24 10:00:17 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/16  

Just to get the tests going again

---

## Comment 1

> Username: apolukhin  
> Created_at: 2018-09-13 17:09:35 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/16#issuecomment-421081475  

This is related to the changes in https://github.com/boostorg/boost/pull/190  
Note that you also have to update all the exceptions in boost/graph/exception.hpp https://github.com/boostorg/graph/blob/4c752ee2f11c249e0d5b1fab8f0ef9da3428f575/include/boost/graph/exception.hpp#L18 by adding `BOOST_SYMBOL_VISIBLE`  
  
See https://www.boost.org/doc/libs/1_65_1/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_for_libraries_with_separate_source_code for more info

---

## Comment 2

> Username: jhunold  
> Created_at: 2018-09-13 18:11:30 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/16#issuecomment-421100939  

Well, this is just to make the unit test compile again (first shot). And yes, I know the relevant macros because I added them a long time ago.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2025-09-24 10:00:17 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/16#issuecomment-3327545409  

So, ummm... is this still relevant, or has it been superseded by other changes?

---
