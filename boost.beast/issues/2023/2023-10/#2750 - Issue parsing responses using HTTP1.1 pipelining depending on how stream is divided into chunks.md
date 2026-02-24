# #2750 - Issue parsing responses using HTTP1.1 pipelining depending on how stream is divided into chunks [Closed]

> Username: Nachalas  
> Created at: 2023-10-17 16:36:37 UTC  
> Updated at: 2024-01-03 07:43:56 UTC  
> Closed at: 2024-01-03 07:43:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2750  

Boost version: 1.80  
  
Steps to reproduce:   
- call a put() method of a response_parser passing a small portion of a response (as may happen in real HTTP1.1 request/response pipelining via net scenario)  
- beast::http::error::need_more as a result with **no bytes saved to an internal buffer of the parser that requires a user to implement complicated logic with additional state management**  
  
So my proposal is to avoid additional external state maintenance in favor of using internal response_parser's state.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-10-17 16:53:36 UTC  
> Updated at: 2023-10-17 16:55:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2750#issuecomment-1766809787  

I'm not enthusiastic about making such a change. Perhaps you can show me example code or suggest the outlines of how the parser's API might be different?

---

## Comment 2

> Username: fpelliccioni  
> Created at: 2023-10-23 13:45:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2750#issuecomment-1775235839  

@Nachalas  
Regarding Vinnie's response, do you have anything more to add or can you provide the example code or details about how the parser's API might differ?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-11-03 03:00:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2750#issuecomment-1791837838  

> beast::http::error::need_more as a result with no bytes saved to an internal buffer of the parser that requires a user to implement complicated logic with additional state management  
  
Are you sure that you are encountering an actual problem? The _DynamicBuffer_ passed to the HTTP read functions does save those bytes. Show the code?

---

## Comment 4

> Username: ashtum  
> Created at: 2024-01-03 07:43:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2750#issuecomment-1874961056  

@Nachalas `put` function returns the number of octets consumed in the buffer. You can consume that amount from the buffer and retry after filling it further. There is no need to maintain any state. You can find the usage in this test case: https://github.com/boostorg/beast/blob/5998feda44d1d0e74945865a8a5a87cc45a2c258/test/beast/http/parser.cpp#L289.  
  
Please feel free to reopen this issue if you find it non-confirming.
