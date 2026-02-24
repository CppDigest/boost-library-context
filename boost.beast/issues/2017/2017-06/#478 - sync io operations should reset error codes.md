# #478 - sync io operations should reset error codes [Closed]

> Username: Inphi  
> Created at: 2017-06-13 03:58:22 UTC  
> Updated at: 2017-06-14 00:38:21 UTC  
> Closed at: 2017-06-14 00:38:21 UTC  
> Url: https://github.com/boostorg/beast/issues/478  

### Version of Beast  
55  
  
When reusing error codes across multiple io operations, beast may terminate the io operation because the error state was previously set. This necessitates the user to always reinitialize the error_code prior to doing io. This behavior is unexpected as boost::asio will reinitialize the error_code before possibly writing to it.  
  
```cpp  
response.body.more = true;  
boost::system::error_code ec;  
auto sr = beast::http::make_serializer(response);  
beast::http::write(sock, sr, ec);  
// ec is now need_buffer  
beast::http::write(sock, sr, ec);  
// write fails here because ec was already set; ec is still need_buffer  
ec = {};  
beast::http::write(sock, serializer, ec);  
// write is good again  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-13 04:32:26 UTC  
> Url: https://github.com/boostorg/beast/issues/478#issuecomment-308004051  

I just had a look through the Asio source code, I think you might be right about this one!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-13 12:04:28 UTC  
> Updated at: 2017-06-13 12:06:28 UTC  
> Url: https://github.com/boostorg/beast/issues/478#issuecomment-308094671  

This is a massive headache because I have to study every function and possibly change it, depending on its logic. I **think** the rule works like this:  
  
If a function has `error_code& ec` as a parameter, it must do one of the following  
  
* Call a function that takes the error code  
* Set the error code to indicate an error  
* Clear the error code  
  
So for example this empty function is wrong:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/empty_body.hpp#L76  
  
It has to clear the error code. *All* empty functions like this need to be changed, and anyone who provides an overload of such function (for example by implementing their own Body or `basic_parser` derived class) has to also apply the rule or risk a regression.  
  
@niklas88 What do you think?

---

## Comment 3

> Username: niklas88  
> Created at: 2017-06-13 12:28:27 UTC  
> Url: https://github.com/boostorg/beast/issues/478#issuecomment-308099992  

Sadly I have absolutely no idea how boost::asio errors are supposed to work, using Beast is actually my first contact with boost::asio.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-13 14:57:11 UTC  
> Url: https://github.com/boostorg/beast/issues/478#issuecomment-308144039  

>Beast is actually my first contact with boost::asio.  
  
Scandalous!
