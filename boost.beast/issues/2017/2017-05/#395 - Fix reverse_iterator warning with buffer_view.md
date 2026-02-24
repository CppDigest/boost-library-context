# #395 - Fix reverse_iterator warning with buffer_view [Closed]

> Username: vinniefalco  
> Created at: 2017-05-28 21:28:51 UTC  
> Updated at: 2018-05-01 18:32:37 UTC  
> Closed at: 2018-05-01 18:32:37 UTC  
> Url: https://github.com/boostorg/beast/issues/395  

gcc generates a spurious(?) uninitialized variable warning when `buffer_view` iterators are used with `std::reverse_iterator`. See buffer_cat.cpp.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:43 UTC  
> Url: https://github.com/boostorg/beast/issues/395#issuecomment-384022668  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-05-01 18:32:28 UTC  
> Url: https://github.com/boostorg/beast/issues/395#issuecomment-385750181  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
