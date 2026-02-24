# #180 - Unreachable code warning on MSVC builds in test matrix [Closed]

> Username: jeking3  
> Created at: 2018-11-11 11:37:58 UTC  
> Updated at: 2019-02-08 17:25:42 UTC  
> Closed at: 2019-02-02 12:45:31 UTC  
> Url: https://github.com/boostorg/test/issues/180  

https://www.boost.org/development/tests/develop/output/teeks99-09-p-win2012R2-64on64-test-msvc-14-1-warnings.html#doc-example22  
  
> d:\teeks99-09\run\boost_root\boost\test\impl\test_tools.ipp(377) : warning C4702: unreachable code  
  
Happens on most of the tests.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-02 12:45:31 UTC  
> Url: https://github.com/boostorg/test/issues/180#issuecomment-459962414  

Resolved via aec7ebe78eee38aab1db75a145171b154f7e6620 and 775114eb5b9baac93e4e72fefdb2e528ac995e82
