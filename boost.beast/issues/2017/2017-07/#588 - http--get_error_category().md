# #588 - http::get_error_category() [Closed]

> Username: vinniefalco  
> Created at: 2017-07-04 02:48:50 UTC  
> Updated at: 2019-04-19 15:20:11 UTC  
> Closed at: 2019-04-18 15:37:48 UTC  
> Url: https://github.com/boostorg/beast/issues/588  

This needs to be a public interface or else there is no way to check if an error code belongs to http! Same with the other categories.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-12 20:07:34 UTC  
> Url: https://github.com/boostorg/beast/issues/588#issuecomment-322002905  

See https://akrzemi1.wordpress.com/2017/08/12/your-own-error-condition/#comment-6954

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:24 UTC  
> Url: https://github.com/boostorg/beast/issues/588#issuecomment-384022558  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-26 20:35:49 UTC  
> Url: https://github.com/boostorg/beast/issues/588#issuecomment-384780479  

HTTP errors should get the same treatment as WebSocket, by adding an error condition and revising all of the error codes to have more verbose messages.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-05-26 21:10:43 UTC  
> Url: https://github.com/boostorg/beast/issues/588#issuecomment-392288354  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: arrtchiu  
> Created at: 2019-04-18 04:44:51 UTC  
> Url: https://github.com/boostorg/beast/issues/588#issuecomment-484354022  

In case someone else runs into this, my workaround for now is this:  
  
```c++  
if (ec == boost::beast::http::make_error_code(boost::beast::http::error::body_limit)) {  
  // do what you need to do when body-limit is exceeded  
}  
```  
  
A bit ugly but for only one error (in my case) that's alright and doesn't rely on the `detail` namespace.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-04-18 15:36:23 UTC  
> Url: https://github.com/boostorg/beast/issues/588#issuecomment-484564492  

What's wrong with  
```  
if (ec == boost::beast::http::error::body_limit)  
```  
?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-04-18 15:37:48 UTC  
> Url: https://github.com/boostorg/beast/issues/588#issuecomment-484565037  

This issue is not an actual problem. There is no need to retrieve the HTTP error category. However, it would be nice to have error codes mapped to error conditions (in the manner of websocket) and to have more descriptive error codes. This is already in another open issue.

---

## Comment 8

> Username: arrtchiu  
> Created at: 2019-04-19 14:05:54 UTC  
> Url: https://github.com/boostorg/beast/issues/588#issuecomment-484906077  

> What's wrong with `if (ec == boost::beast::http::error::body_limit)`  
  
Good point, I must've been having a slow brain day!  
  
If I was handling more of these error codes, being able to use a `switch` statement would be useful - something like this:  
  
```c++  
if (ec.category() == boost::beast::http::get_error_category()) {  
  switch (static_cast<boost::beast::http::error>(ec.value()) {  
    //...  
  }  
}  
```  
  
I'm reasonably new to boost/asio so maybe this kind of pattern is frowned upon, though. Open to feedback if you've got the time to spare :)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-04-19 15:20:11 UTC  
> Url: https://github.com/boostorg/beast/issues/588#issuecomment-484928789  

```  
if(ec.category() == beast::error_code(static_cast<beast::http::error>(0)).category())  
    ...  
```
