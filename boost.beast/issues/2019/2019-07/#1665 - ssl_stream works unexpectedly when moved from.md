# #1665 - ssl_stream works unexpectedly when moved from [Closed]

> Username: mitchellwong  
> Created at: 2019-07-23 20:06:24 UTC  
> Updated at: 2019-10-06 21:10:24 UTC  
> Closed at: 2019-10-06 21:10:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1665  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
248  
  
### Steps necessary to reproduce the problem  
  
ssl_stream first{next_stream, ctx};  
ssl_stream second{move(first)};  
  
auto next_layer = first.next_layer(); //return reference to nullptr  
  
The standard says in 20.5.3.1 a moved from object must still meet the requirements of the library that's using it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-23 20:08:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1665#issuecomment-514364435  

> The standard says in 20.5.3.1 a moved from object must still meet the requirements of the library that's using it.  
  
Yes, that applies to objects **provided by the standard**. `beast::ssl_stream` is not provided by the standard, and so does not need to follow that rule.

---

## Comment 2

> Username: mitchellwong  
> Created at: 2019-07-23 20:45:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1665#issuecomment-514376826  

But `beast::ssl_stream` says that it satisfies [MoveConstructible](http://eel.is/c++draft/concept.moveconstructible), so isn't it opting into being constrained by that rule?

---

## Comment 3

> Username: djarek  
> Created at: 2019-07-23 20:54:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1665#issuecomment-514380117  

> rv's resulting state is valid but unspecified  
  
Valid means the user can either destroy the object or that he can perform any operation that has no precondition,

---

## Comment 4

> Username: mitchellwong  
> Created at: 2019-07-31 17:41:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1665#issuecomment-516949578  

If not being moved from is a prerequisite to all of the members, it'd be helpful if that were documented. I was very surprised on finding this when debugging.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-07-31 19:04:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1665#issuecomment-516979880  

I agree, it could have better docs, would you like to make an addition? It could be added to this javadoc comment:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/ssl/ssl_stream.hpp#L33

---

## Comment 6

> Username: stale[bot]  
> Created at: 2019-08-30 19:46:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1665#issuecomment-526725663  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: mitchellwong  
> Created at: 2019-08-30 19:54:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1665#issuecomment-526728017  

Sorry, I've been busy at work lately. Should I also add that to `websocket::stream`? https://github.com/boostorg/beast/issues/1566

---

## Comment 8

> Username: stale[bot]  
> Created at: 2019-09-29 20:28:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1665#issuecomment-536338278  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created at: 2019-10-06 21:10:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1665#issuecomment-538788407  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
