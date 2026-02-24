# #359 - questions about beast::websocket::close_reason [Closed]

> Username: vchang-akamai  
> Created at: 2017-05-12 23:09:53 UTC  
> Updated at: 2020-04-23 00:29:08 UTC  
> Closed at: 2017-05-14 23:01:53 UTC  
> Url: https://github.com/boostorg/beast/issues/359  

1) I noticed these two constructors for beast::websocket::close_reason:  
```  
    /// Construct from a code.  
    close_reason(std::uint16_t code_)  
        : code(code_)  
    {  
    }  
```  
```  
    /// Construct from a code and reason.  
    template<std::size_t N>  
    close_reason(close_code code_,  
            char const (&reason_)[N])  
        : code(code_)  
        , reason(reason_)  
    {  
    }  
```  
There seems to be an inconsistency for the "code_" parameter.  The first one uses uint16_t for code_ while the second one uses close_code for code_.  close_code is an enum, so that's signed integer.   
  
2) Two of the constructors take a 'char const (&)[N]' for the reason_ parameter.  Would be possible to provide a std::string version as well?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-12 23:40:06 UTC  
> Updated at: 2017-05-13 16:52:35 UTC  
> Url: https://github.com/boostorg/beast/issues/359#issuecomment-301208786  

Hmm, you are right. They should both use a regular integer, probably unsigned since thats what the WebSocket protocol specifies.  
  
I don't think `std::string` is a particularly good choice of parameter but `beast::string_view` would be an improvement, and it would still let you pass a `std::string` (`string_view` is constructible from `std::string`).  
  
These are good observations, thanks! I will make these changes.

---

## Comment 2

> Username: chreniuc  
> Created at: 2019-11-22 13:18:43 UTC  
> Url: https://github.com/boostorg/beast/issues/359#issuecomment-557528313  

@vinniefalco   
  
How does one provide a custom close code(user defined, just like the [websocket specs say](https://tools.ietf.org/html/rfc6455#section-7.4.2), range 4000-4999) and a close reason in the async_close method?  
  
Doing this:  
  
```c++  
close_reason = make_shared<::boost::beast::websocket::close_reason>(  
        static_cast<::std::uint16_t>(4001 ), "Close reason."s )   
  
ws.async_close(  
    *close_reason ,  
    std::bind( &ws_server::on_close_websocket, this, conn_ptr, close_reason,  
      std::placeholders::_1 ) );  
```  
  
I get a compile error:  
  
```bash  
error: invalid conversion from ‘short unsigned int’ to ‘boost::beast::websocket::close_code’ [-fpermissive]  
```  
  
But creating the close_reason with ony the close_code works:  
  
```c++  
close_reason = make_shared<::boost::beast::websocket::close_reason>(  
        static_cast<::std::uint16_t>(4001 ) )   
  
ws.async_close(  
    *close_reason ,  
    std::bind( &ws_server::on_close_websocket, this, conn_ptr, close_reason,  
      std::placeholders::_1 ) );  
```  
The first method calls this constructor:  
```c++  
close_reason(close_code code_, string_view s)  
```  
Where `close_code` is this enum:  
  
```c++  
enum close_code : std::uint16_t  
{  
    /// Normal closure; the connection successfully completed whatever purpose for which it was created.  
    normal          = 1000  
 //....  
}  
```  
  
And the second method calls this constructor:  
  
```c++  
 close_reason(std::uint16_t code_)  
```  
  
Shouldn't we be able to use custom close codes and close reasons?

---

## Comment 3

> Username: chreniuc  
> Created at: 2019-11-22 13:26:49 UTC  
> Url: https://github.com/boostorg/beast/issues/359#issuecomment-557531002  

I forgot to mention the boost version: it's 1.70.0

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-11-22 14:07:50 UTC  
> Url: https://github.com/boostorg/beast/issues/359#issuecomment-557545014  

cast to a close code not a `uint16_t`

---

## Comment 5

> Username: ceaglest  
> Created at: 2020-04-23 00:29:08 UTC  
> Url: https://github.com/boostorg/beast/issues/359#issuecomment-618108070  

Thank you, this issue was also helpful for me to understand how to provide a custom `beast::websocket::close_reason`.
