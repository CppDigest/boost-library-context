# #464 - Respect Allocator::max_size in flat_buffer, multi_buffer [Closed]

> Username: vinniefalco  
> Created at: 2017-06-10 17:48:27 UTC  
> Updated at: 2018-05-01 18:32:39 UTC  
> Closed at: 2018-05-01 18:32:39 UTC  
> Url: https://github.com/boostorg/beast/issues/464  

Pretty easy just construct `max_` as `std::min(Allocator::max_size(), limit)`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-10 17:56:36 UTC  
> Url: https://github.com/boostorg/beast/issues/464#issuecomment-307580593  

_"If the two allocator instances compare equal but their max_size() is different? Seems already broken to me - you could just handle it as though they did not compare equal."_

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:38 UTC  
> Url: https://github.com/boostorg/beast/issues/464#issuecomment-384022647  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-05-01 18:32:30 UTC  
> Url: https://github.com/boostorg/beast/issues/464#issuecomment-385750195  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
