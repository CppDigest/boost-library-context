# #1935 - User-Agent HTTP header shouldn't be added if explicitly removed through decorator [Closed]

> Username: akiirax  
> Created at: 2020-05-03 19:28:35 UTC  
> Updated at: 2020-09-15 07:22:36 UTC  
> Closed at: 2020-05-05 00:18:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1935  

### Version of Beast  
`Beast version 292`  
  
### Steps necessary to reproduce the problem  
```  
stream->set_option(websocket::stream_base::decorator(  
            [](websocket::request_type &req) {  
                req.erase(http::field::user_agent);  
            }));  
```  
  
This results in the client sending `User-Agent: Boost.Beast/290` along the request.  
  
@vinniefalco defined this behavior caused by https://github.com/boostorg/beast/blob/718aef721a7f966ed37c4be28c5d5d92cd21966f/include/boost/beast/websocket/impl/stream_impl.hpp#L591  
  
which shouldn't be there in the first place as User-Agent HTTP header isn't mandatory.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-03 19:36:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1935#issuecomment-623168448  

We should just remove the Beast feature that fills in the User-Agent.
