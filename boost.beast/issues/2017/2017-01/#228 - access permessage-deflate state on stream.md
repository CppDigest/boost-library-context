# #228 - access permessage-deflate state on stream [Closed]

> Username: vinniefalco  
> Created at: 2017-01-18 22:19:32 UTC  
> Updated at: 2025-04-27 16:44:33 UTC  
> Closed at: 2025-04-27 16:44:33 UTC  
> Url: https://github.com/boostorg/beast/issues/228  

It should be possible to query the stream, after a successful handshake, to determine the permessage-deflate negotiation status. Example:  
```  
struct permessage_deflate_status  
{  
    bool active;  
    int client_window_bits;  
    int server_window_bits;  
};  
  
void  
stream::get_status(permessage_deflate_status& status);  
```
