# #2708 - How to stream HTTP response as a `std::basic_istream` (or something like that) [Closed]

> Username: jviotti  
> Created at: 2023-07-24 23:42:40 UTC  
> Updated at: 2023-08-21 13:09:44 UTC  
> Closed at: 2023-08-21 13:09:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2708  

Hey there! I'm looking for an example on how I can perform an HTTP request, but instead of getting the response as a single `std::string`, get it as it comes as a standard input stream or something like that.  
  
Do you have an examples at hand?  
  
### Version of Beast  
  
I'm on version 347.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-07-25 00:40:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2708#issuecomment-1648802839  

We do not. You could just use a stringstream to wrap an existing string-body I think.

---

## Comment 2

> Username: ashtum  
> Created at: 2023-08-19 05:55:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2708#issuecomment-1684841893  

@jviotti is this still open?

---

## Comment 3

> Username: jviotti  
> Created at: 2023-08-20 15:59:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2708#issuecomment-1685318844  

@ashtum , as @klemens-morgenstern mentioned, this is not supported at the moment.

---

## Comment 4

> Username: ashtum  
> Created at: 2023-08-21 08:30:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2708#issuecomment-1685887116  

@jviotti You can adapt your own custom type as a message body, here is an example that read and write to/from an `asio::streambuf`: https://godbolt.org/z/hEG6eMq4Y

---

## Comment 5

> Username: jviotti  
> Created at: 2023-08-21 13:09:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2708#issuecomment-1686300025  

Interesting. Thanks! I'll close this then!
