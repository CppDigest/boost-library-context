# #2218 - how to download the bigdata response that type content type is application/octet-stream [Closed]

> Username: nulls-cell  
> Created at: 2021-04-20 10:34:16 UTC  
> Updated at: 2022-01-09 05:17:26 UTC  
> Closed at: 2022-01-09 05:17:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2218  

he file is very large and the local memory cannot be fully loaded, so how read it as a iterator.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-04-20 14:14:45 UTC  
> Updated at: 2021-04-20 14:14:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2218#issuecomment-823309592  

First read the header:  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html  
  
And then while `!parser.is_done()`, read sections and handle them incrementally:  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2218#issuecomment-850857839  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2022-01-09 05:17:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2218#issuecomment-1008232418  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
