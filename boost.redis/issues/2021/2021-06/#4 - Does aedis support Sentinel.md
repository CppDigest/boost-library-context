# #4 - Does aedis support Sentinel? [Closed]

> Username: portsip  
> Created at: 2021-06-14 13:55:22 UTC  
> Updated at: 2025-06-21 11:26:56 UTC  
> Closed at: 2022-10-02 08:00:30 UTC  
> Url: https://github.com/boostorg/redis/issues/4  

Hi, does aedis support Sentinel?

---

## Comment 1

> Username: mzimbres  
> Created at: 2021-06-14 17:27:18 UTC  
> Url: https://github.com/boostorg/redis/issues/4#issuecomment-860859816  

Hi, it used to support sentinel. After a major refactoring however I  
removed it and didn't implement again as I don't need it in my server. Some  
commands are also missing.  
  
Marcelo  
  
Em seg., 14 de jun. de 2021 15:55, portsip ***@***.***>  
escreveu:  
  
> Hi, does aedis support Sentinel?  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/mzimbres/aedis/issues/4>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAA3DKBBUMTSR5LPYZV66OTTSYC5ZANCNFSM46VJFXDA>  
> .  
>

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-09-24 09:45:41 UTC  
> Url: https://github.com/boostorg/redis/issues/4#issuecomment-1256926260  

An example that shows how to use sentinel can be seen in this example [subscriber_sentinel](examples/subscriber_sentinel.cpp).
