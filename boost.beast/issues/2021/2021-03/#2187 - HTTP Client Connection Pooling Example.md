# #2187 - HTTP Client Connection Pooling Example [Closed]

> Username: LukeMauldin  
> Created at: 2021-03-12 22:54:49 UTC  
> Updated at: 2022-01-09 05:17:21 UTC  
> Closed at: 2022-01-09 05:17:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2187  

### Version of Beast  
  
1.74  
  
### Steps necessary to reproduce the problem  
  
I am using boost beast HTTP client to make HTTP and HTTPS requests. Is there an example of how to use boost beast with connection pooling and keep-alive so that if multiple HTTP requests are made to the same URL, then the TCP connections will be reused?  
Since beast is a fairly low-level library, I have abstracted it away from my higher level application components.  I would like automated back-ground connection pooling similar to the Go standard's library `net/http` package if that is possible.  
  
### All relevant compiler information  
  
Compiler: MSVC 2019 update 9

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-12 22:56:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2187#issuecomment-797801807  

When I got the notification, I was hoping this would be a pull request with a fully working example of the aforementioned features. But alas, it is only a feature request.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-03-13 00:11:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2187#issuecomment-797826136  

I wrote a blog on it in the New Year  
  
https://cppalliance.org/richard/2021/01/01/RichardsNewYearUpdate.html

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2187#issuecomment-850857858  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2187#issuecomment-1008232407  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
