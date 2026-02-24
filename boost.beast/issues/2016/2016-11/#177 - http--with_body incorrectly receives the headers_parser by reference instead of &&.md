# #177 - http::with_body incorrectly receives the headers_parser by reference instead of && [Closed]

> Username: georgi-d  
> Created at: 2016-11-09 14:45:33 UTC  
> Updated at: 2016-11-12 17:42:46 UTC  
> Closed at: 2016-11-12 17:42:46 UTC  
> Url: https://github.com/boostorg/beast/issues/177  

The implementation of http::with_body conflicts with its documentation because it takes the header_parser by reference instead of &&.   
  
The example:  
  
`auto p = with_body<string_body>(std::move(ph));`  
  
cannot work because the result of std::move cannot bind to a non-const reference.  
  
The implementation of with_body does the move itself at the moment but I think it would be better for the move to be as in the example when calling with_body

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-09 14:54:04 UTC  
> Updated at: 2016-11-09 14:54:56 UTC  
> Url: https://github.com/boostorg/beast/issues/177#issuecomment-259432862  

I see, so you're saying it should look like this:  
  
```  
template<class Body, bool isRequest, class Headers, class... Args>  
parser_v1<isRequest, Body, Headers>  
with_body(headers_parser_v1<isRequest, Headers>&& parser,  
    Args&&... args)  
{  
    return parser_v1<isRequest, Body, Headers>(  
        std::move(parser), std::forward<Args>(args)...);  
}  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-11-09 15:14:53 UTC  
> Url: https://github.com/boostorg/beast/issues/177#issuecomment-259437925  

Sorry, no, that's not how its supposed to work. The example code is wrong. It should read:  
  
```  
auto p = with_body<string_body>(ph);  
```  
  
It is not necessary to move the headers parser.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-11-09 15:18:48 UTC  
> Url: https://github.com/boostorg/beast/issues/177#issuecomment-259439008  

I believe this is the correct fix: https://github.com/vinniefalco/Beast/commit/b968887b642f6b53b276648068d4efa88d895ae2

---

## Comment 4

> Username: georgi-d  
> Created at: 2016-11-09 16:05:53 UTC  
> Url: https://github.com/boostorg/beast/issues/177#issuecomment-259451594  

I thought the comment should stay as is and the implementation change to:  
  
```  
  
template<class Body, bool isRequest, class Headers, class... Args>  
parser_v1<isRequest, Body, Headers>  
with_body(headers_parser_v1<isRequest, Headers>&& parser,  
    Args&&... args)  
{  
    return parser_v1<isRequest, Body, Headers>(  
        std::forward(parser), std::forward<Args>(args)...);  
}  
```  
  
The implicit move inside with_body() feels surprising to me, I feel it would be better to be explicit at the call site of with_body.  
  
Do you have a use case where the headers parser would not be moved in the end?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-11-09 16:07:44 UTC  
> Updated at: 2016-11-09 16:08:45 UTC  
> Url: https://github.com/boostorg/beast/issues/177#issuecomment-259452073  

> Do you have a use case where the headers parser would not be moved in the end?  
  
The `headers_parser` is never moved. Only the `message` inside is moved, as if by `parser.release()`. The parser is a stateless POD type object, there's nothing to move except for the message.  
  
See:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/parser_v1.hpp#L131

---

## Comment 6

> Username: georgi-d  
> Created at: 2016-11-09 16:14:38 UTC  
> Url: https://github.com/boostorg/beast/issues/177#issuecomment-259453914  

The current implementation  moves the parser:  
  
return parser_v1<isRequest, Body, Headers>( **std::move(parser)**, std::forward<Args>(args)...);  
  
If the move is not needed then the code should be:  
  
```  
  
template<class Body, bool isRequest, class Headers, class... Args>  
parser_v1<isRequest, Body, Headers>  
with_body(headers_parser_v1<isRequest, Headers>& parser,  
    Args&&... args)  
{  
    return parser_v1<isRequest, Body, Headers>(  
        parser, std::forward<Args>(args)...);  
}  
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-11-09 16:15:53 UTC  
> Url: https://github.com/boostorg/beast/issues/177#issuecomment-259454265  

oops... Yes, you are correct. will fix

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-11-09 16:57:33 UTC  
> Updated at: 2016-11-09 16:57:40 UTC  
> Url: https://github.com/boostorg/beast/issues/177#issuecomment-259465558  

@georgi-d Let me tell you, this little problem turned out to be a huge pain.  
This is the latest version of the fix: https://github.com/vinniefalco/Beast/commit/b5432f8e0da8ee841b2da34bb418ef61ed0aace3

---

## Comment 9

> Username: georgi-d  
> Created at: 2016-11-09 17:19:56 UTC  
> Url: https://github.com/boostorg/beast/issues/177#issuecomment-259471104  

This version looks good to me.  
  
Thanks
