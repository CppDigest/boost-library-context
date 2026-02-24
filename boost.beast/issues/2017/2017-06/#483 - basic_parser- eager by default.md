# #483 - basic_parser: eager by default? [Closed]

> Username: vinniefalco  
> Created at: 2017-06-13 12:21:42 UTC  
> Updated at: 2017-06-20 01:50:26 UTC  
> Closed at: 2017-06-20 01:50:26 UTC  
> Url: https://github.com/boostorg/beast/issues/483  

Should `basic_parser` default to eager?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-20 01:50:26 UTC  
> Url: https://github.com/boostorg/beast/issues/483#issuecomment-309621476  

I'm guessing no, because all but the most trivial of use cases require reading the header first, and for the trivial use cases they can just use the message oriented functions.
