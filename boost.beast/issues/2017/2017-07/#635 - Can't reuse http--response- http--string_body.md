# #635 - Can't reuse http::response< http::string_body > [Closed]

> Username: ksergey  
> Created at: 2017-07-11 12:06:26 UTC  
> Updated at: 2017-07-12 08:43:00 UTC  
> Closed at: 2017-07-12 08:43:00 UTC  
> Url: https://github.com/boostorg/beast/issues/635  

Hello,  
  
Looks like http::response body not cleared during executing http request. Is it expected behaviour? Is `http::response<...>` is reusable object?  
  
Thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-11 12:33:56 UTC  
> Url: https://github.com/boostorg/beast/issues/635#issuecomment-314429109  

Are you asking if `beast::http::write` clears the body after sending the message? No, it does not. The function accepts a `const&`, it cannot modify the message.  
  
Why do you want to clear the message? Maybe its something I can add. If you want to reuse the variable you could put it in a `boost::optional` and just `emplace` it every time you want to use it.

---

## Comment 2

> Username: ksergey  
> Created at: 2017-07-12 08:43:00 UTC  
> Url: https://github.com/boostorg/beast/issues/635#issuecomment-314696836  

@vinniefalco thanks, boost::optional is solution for me
