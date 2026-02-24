# #1625 - Parser Stream Operations documentation references wrong Stream types [Open]

> Username: bsergeev  
> Created at: 2019-05-29 01:01:07 UTC  
> Updated at: 2019-06-06 01:27:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1625  

### Version of Beast  
1.70  
  
### Steps necessary to reproduce the problem  
Open  
https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/using_http/parser_stream_operations.html  
  
## Problem:  
5 out of 6 read functions listed in Table 1.22, _Parser Stream Operations_, refer to `WriteStream`, while they should refer to `ReadStream`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-06-06 01:27:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1625#issuecomment-499314280  

Yep, they are flipped
