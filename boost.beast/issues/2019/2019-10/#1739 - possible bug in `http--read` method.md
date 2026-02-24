# #1739 - possible bug in `http::read` method [Closed]

> Username: romange  
> Created at: 2019-10-21 19:48:34 UTC  
> Updated at: 2019-10-21 22:00:28 UTC  
> Closed at: 2019-10-21 22:00:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1739  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
1.71  
### Steps necessary to reproduce the problem  
Unfortunately this happens relatively rarely and very hard to reproduce.  
I am running on linux 18.04 compiled with gcc7.  
I have an instance of parser of type `h2::response_parser<h2::buffer_body>`  
and I call `beast::http::read(*client_, tmp_buffer_, parser, ec);`  
to read from a client socket. my `buffer_body.size` is 4096 bytes.   
  
Expected: to get "body.size=0" if parser is not done and the status is error::partial_message. (I am assuming that `read()` differs from `read_some()` by guaranteeing to fill up the body if possible) - please tell me if I am wrong.  
  
Actual:  parser is not done and the status is `error::partial_message` and buffer_body.size is some positive number.  
  
Please note that sometimes the same response is parsed correctly, the behavior depends on how data flows on the socket and it requires gigabytes of data to trigger this condition.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-21 19:50:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1739#issuecomment-544678495  

You have to use `read_some`. See: https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__http__read/overload1.html

---

## Comment 2

> Username: romange  
> Created at: 2019-10-21 19:55:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1739#issuecomment-544680311  

I do not understand. What's the difference between `read_some` and `read` ?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-21 20:31:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1739#issuecomment-544694672  

`read` assumes that it will see the entire message, which is not possible with `buffer_body`.

---

## Comment 4

> Username: romange  
> Created at: 2019-10-21 22:00:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1739#issuecomment-544726323  

It's a bug in my socket code, sorry for false alarm
