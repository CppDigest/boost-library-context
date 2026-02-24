# #113 - Some sections of ASIO (and others?) documentation lack an "index" [Closed]

> Username: reddwarf69  
> Created at: 2018-05-11 11:49:01 UTC  
> Updated at: 2020-12-30 00:51:31 UTC  
> Closed at: 2020-12-30 00:51:30 UTC  
> Url: https://github.com/boostorg/asio/issues/113  

No idea how Quickbook is supposed to work. But from the perspective of somebody reading the docs:  
  
- I'm in https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference.html  
- I click on "Asynchronous operations" (under "Type Requirements")  
- I get to https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/asynchronous_operations.html  
  
And here I would have expected an index. It took me ages to notice that I can now click on the right arrow to reach https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/asynchronous_operations/general_asynchronous_operation_concepts.html (AFAIK the only way to reach it).  
  
Not sure if it's an issue with Quickbook (so affecting others?) or with the way ASIO uses Quickbook.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-07 22:45:12 UTC  
> Url: https://github.com/boostorg/asio/issues/113#issuecomment-395589149  

The problem is that if indexing is enabled, then it will produce a table of contents above each quick reference page, which would be terrible.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:51:29 UTC  
> Url: https://github.com/boostorg/asio/issues/113#issuecomment-752289551  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#618](https://github.com/chriskohlhoff/asio/issues/618).
