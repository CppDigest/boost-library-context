# #15 - When using mqtt over websocket, how can I specify the http request path? [Closed]

> Username: 0xFEEDC0DE64  
> Created at: 2024-10-08 10:15:20 UTC  
> Updated at: 2024-11-19 17:14:32 UTC  
> Closed at: 2024-11-19 17:14:31 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/15  

I am running my mqtt websocket server behind a reverse proxy which only makes it available on  /test, but not on / (there my homepage runs html code, but no mqtt).  
  
How can I specify the http request parameters with this mqtt library when using boost beast?

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-10-08 11:11:23 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/15#issuecomment-2399552910  

Hi, have you tried specifying the path to your server using the `mqtt_client::brokers` function like this?  
```  
async_mqtt5::mqtt_client<...> client(...);  
client.brokers("localhost/test", port);  
```

---

## Comment 2

> Username: 0xFEEDC0DE64  
> Created at: 2024-11-19 17:14:32 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/15#issuecomment-2486295819  

thanks! I think that worked
