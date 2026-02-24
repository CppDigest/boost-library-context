# #92 - Example: reconnect [Closed]

> Username: anarthal  
> Created at: 2022-05-19 21:05:42 UTC  
> Updated at: 2024-08-10 16:21:06 UTC  
> Closed at: 2024-08-10 16:21:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/92  

Provide an example on how to reconnect a failed MySQL connection (e.g. due to a network failure).

---

## Comment 1

> Username: Mecanik  
> Created at: 2022-12-09 10:47:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/92#issuecomment-1344145453  

Where is this example? Thanks a lot.

---

## Comment 2

> Username: anarthal  
> Created at: 2022-12-09 18:00:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/92#issuecomment-1344601900  

I'm afraid this hasn't been done yet. There is a section on reconnection in the docs that explains the basics, which you may find useful: https://anarthal.github.io/mysql/mysql/reconnecting.html.  
  
If you encounter any problem, please feel free to contact me for help.  
  
Regards,  
Ruben.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-08-10 16:21:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/92#issuecomment-2282199607  

`connection_pool` already does this out-of-the-box, and with `any_connection`, this should be trivial. I don't think we need this anymore.
