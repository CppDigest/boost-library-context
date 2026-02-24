# #1236 - The Writing Composed Operations documentation says handlers are required to be  CopyConstructible [Closed]

> Username: reddwarf69  
> Created at: 2018-08-27 20:50:57 UTC  
> Updated at: 2019-04-19 03:10:16 UTC  
> Closed at: 2019-04-19 03:10:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1236  

Reading https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_io/writing_composed_operations.html I can see  
  
```  
// The operation's data is kept in a cheap-to-copy smart  
// pointer container called `handler_ptr`. This efficiently  
// satisfies the CopyConstructible requirements of completion  
// handlers with expensive-to-copy state.  
```  
  
and  
  
```  
// Boost.Asio requires that handlers are CopyConstructible.  
```  
  
But https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/reference/Handler.html says `A handler must meet the requirements of MoveConstructible types` and https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/reference/CompletionHandler.html doesn't add a CopyConstructible requirement.  
Only in https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/reference/LegacyCompletionHandler.html it says `A legacy completion handler must meet the requirements of CopyConstructible types`.

---

## Comment 1

> Username: djarek  
> Created at: 2018-08-27 21:00:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1236#issuecomment-416367181  

This is most likely a leftover from old versions in which CompletionHandlers had to be CopyConstructible. All CompletionHandlers used by Beast don't need to be CopyConstructible, MoveConstructible is sufficient (same is true for all non-legacy APIs in ASIO).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-08-27 21:04:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1236#issuecomment-416368211  

Will fix, thanks!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-23 03:42:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1236#issuecomment-466612927  

This should be fixed now in master and develop
