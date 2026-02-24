# #234 - How do I put a beast::websocket::stream in a class variable? [Closed]

> Username: UndarkAido  
> Created at: 2017-01-22 05:56:35 UTC  
> Updated at: 2017-01-22 12:28:38 UTC  
> Closed at: 2017-01-22 10:39:14 UTC  
> Url: https://github.com/boostorg/beast/issues/234  

I create it using `beast::websocket::stream<stream_type&> ws{stream};`, and I have a class variable `std::shared_ptr<beast::websocket::stream<stream_type&>> ws_;` but trying to assign it results in results in:  
```  
error: no viable overloaded '='  
    ws_ = &ws;  
    ~~~ ^ ~~~  
```  
Which I didn't think was a good idea anyway. I tried creating it as a shared_ptr with `std::shared_ptr<beast::websocket::stream<stream_type&>>ws = {stream};`, `std::shared_ptr<beast::websocket::stream<stream_type&>>ws{stream};`,  
```cpp  
    std::shared_ptr<beast::websocket::stream<stream_type&>>ws;  
    *ws{stream};  
```  
, and   
```cpp  
    std::shared_ptr<beast::websocket::stream<stream_type&>>ws;  
    ws->{stream};  
```  
but none of those worked. I have no idea what I'm doing. DOes anyone have any help?

---

## Comment 1

> Username: UndarkAido  
> Created at: 2017-01-22 08:55:19 UTC  
> Updated at: 2017-01-22 08:57:12 UTC  
> Url: https://github.com/boostorg/beast/issues/234#issuecomment-274318174  

I ended up doing this: `ws_ = new beast::websocket::stream<stream_type&>(stream);`  
  
If that's wrong, please tell me.  
  
Edit: Nope, that didn't work.

---

## Comment 2

> Username: UndarkAido  
> Created at: 2017-01-22 10:39:14 UTC  
> Url: https://github.com/boostorg/beast/issues/234#issuecomment-274322968  

I did this, and I'm pretty sure it's fine. `ws_ = std::make_shared<beast::websocket::stream<stream_type&>>(stream);`

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-01-22 12:28:38 UTC  
> Url: https://github.com/boostorg/beast/issues/234#issuecomment-274328004  

Yep, that works, and I was going to suggest it.
