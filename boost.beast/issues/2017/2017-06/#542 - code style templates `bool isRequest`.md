# #542 - code style templates `bool isRequest` [Closed]

> Username: qwertzui11  
> Created at: 2017-06-26 08:08:04 UTC  
> Updated at: 2017-06-26 15:33:12 UTC  
> Closed at: 2017-06-26 15:29:37 UTC  
> Url: https://github.com/boostorg/beast/issues/542  

### Version of Beast  
v67  
  
### issue  
throughout the library you use  
`template<bool isRequest, [...]`  
which results in  
`beast::http::parser<true, [...]>`  
wouldn't it be cleaner if it'd be  
`template<enum Type, [...]`  
with the result  
`beast::http::parser<beast::http::type::request, [...]>`  
https://blog.ometer.com/2011/01/20/boolean-parameters-are-wrong/  
> Robert C. Martin's Clean Code Tip #12: Eliminate Boolean Arguments

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-26 13:43:45 UTC  
> Url: https://github.com/boostorg/beast/issues/542#issuecomment-311063093  

You want to add `enum type` to `beast::http::`?

---

## Comment 2

> Username: qwertzui11  
> Created at: 2017-06-26 14:53:17 UTC  
> Url: https://github.com/boostorg/beast/issues/542#issuecomment-311083246  

no. I'm suggesting, that using true/false for the message type may gets interpreted as "bad practice".

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-26 15:26:16 UTC  
> Updated at: 2017-06-26 15:27:16 UTC  
> Url: https://github.com/boostorg/beast/issues/542#issuecomment-311093210  

Well you're not the first to complain :) I rather like `bool`. Its concise and expresses the dual nature of messages quite well. I have no desire to replace `bool`, `true`, and `false` with `beast::http::kind`, `beast::http::kind::request`, and `beast::http::kind::response`. You can use your own constants if you'd like:  
```  
bool constexpr beast_request_message_kind = true;  
bool constexpr beast_response_message_kind = false;  
```

---

## Comment 4

> Username: qwertzui11  
> Created at: 2017-06-26 15:29:37 UTC  
> Url: https://github.com/boostorg/beast/issues/542#issuecomment-311094220  

check, asked and answered :smile:

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-26 15:33:12 UTC  
> Url: https://github.com/boostorg/beast/issues/542#issuecomment-311095245  

If you feel strongly about it, consider participating in the Boost formal review! Just sign up at the Boost developers mailing list. Any comments or criticisms are welcome. You can raise this point, but if you would offer a post with your experiences using the library (good or bad) that would be **extremely** helpful not only for the formal review process but also to help me make the library better at suiting everyone's needs. Sign up here:  
http://www.boost.org/community/groups.html#main
