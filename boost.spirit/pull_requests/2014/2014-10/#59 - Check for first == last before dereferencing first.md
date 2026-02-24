# #59 Check for first == last before dereferencing first [Merged]

> Username: aiche  
> Created at: 2014-10-13 08:26:56 UTC  
> Updated at: 2014-10-13 10:22:09 UTC  
> Merged at: 2014-10-13 10:21:40 UTC  
> Closed at: 2014-10-13 10:21:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/59  

Trac ticket [#6955](https://svn.boost.org/trac/boost/ticket/6955) describes an issue with dereferencing the past-the-end iterator in `parse_nan`. For details see https://svn.boost.org/trac/boost/ticket/6955. This pull request fixes this issue by checking `first != last` before dereferencing the `first` iterator.

---
