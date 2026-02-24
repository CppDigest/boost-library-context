# #1658 - string_view returned from the request headers has extra \r\n chars [Closed]

> Username: oAly  
> Created at: 2019-07-16 15:12:17 UTC  
> Updated at: 2019-08-22 16:32:05 UTC  
> Closed at: 2019-08-22 16:32:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1658  

Hey there,  
  
So whenever I send an HTTP request to my beast server and request the headers from request.base(), the function returns a string_view and appends a /r/n at the end. Is there a reason for that?  
  
I am using the 1.69 Beast version

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-16 15:28:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1658#issuecomment-511866142  

A `string_view` representing an HTTP field name or HTTP field value does not contain a CRLF at the end. Perhaps you are looking past the end of the string? Please provide an example where CRLF (`"/r/n"`) appears at the end of a returned `string_view`. Make sure you also include the value returned by calling `string_view::size()`, in addition to all of the bytes pointed to by `string_view::data()`.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-08-15 16:10:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1658#issuecomment-521698710  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-08-22 16:32:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1658#issuecomment-523980208  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
