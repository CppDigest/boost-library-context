# #34 - how to identify call for  async result? [Closed]

> Username: MilamberValheru  
> Created at: 2025-07-08 08:36:56 UTC  
> Updated at: 2025-07-08 09:05:36 UTC  
> Closed at: 2025-07-08 09:05:36 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/34  

Hi,  
  
when I start more than one client.async_publish:   
  
I get the results in a handler "[](boost::mqtt5::error_code ec)"  
  
How can I find out to which call the result belongs?

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-07-08 08:42:50 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/34#issuecomment-3047935299  

You can put extra information in the lambda capture context, you can see an example here: https://github.com/boostorg/mqtt5/blob/272217cbb3eb4c865f0d8cd8b67c055f56c55b88/example/multiflight_client.cpp#L51-L60

---

## Comment 2

> Username: MilamberValheru  
> Created at: 2025-07-08 09:05:33 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/34#issuecomment-3048014266  

🙏
