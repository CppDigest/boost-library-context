# #313 - caching_sha2_password should not require SSL [Closed]

> Username: anarthal  
> Created at: 2024-07-11 15:37:46 UTC  
> Updated at: 2025-06-07 11:20:49 UTC  
> Closed at: 2025-06-07 11:20:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/313  

We can request the server's public key and encrypt the password with it instead of failing the operation. This requires a significant refactor in the handshake code.

---

## Comment 1

> Username: vlaben  
> Created at: 2024-07-13 20:45:51 UTC  
> Updated at: 2024-07-13 20:52:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/313#issuecomment-2227099488  

My game server sending data to database via DLL would benefit from this improvement :)   
(the game server and the MySQL server runs on the same machine).  
  
An encrypted connection on a local network would be overkill IMHO -  
an unencrypted connection established using an RSA key pair for  
password exchange would satisfy `caching_sha2_password `  
requirements (win - TCP/IP).  
  
Right now, we still have the option to use `mysql_native_password`,  
even after changes 8.4. However, this option is deprecated.  
  
In other words - implementing this enhancement would be a great solution  
in a local network client-server scenario, where the default authentication  
plugin is used, but an unencrypted connection is preferred.  
  
One more important thing - Thank you sir for all your work. 🥇  
I really appreciate the library!

---

## Comment 2

> Username: anarthal  
> Created at: 2024-07-13 21:46:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/313#issuecomment-2227111397  

Thanks.  
  
It's actually removed in 9.0, which is causing terrible pain in CIs.  
  
I agree that'd be useful in your case, but if you're doing Windows specific stuff, you'll find named pipes (#89) faster. Which I should code someday, too :)

---

## Comment 3

> Username: anarthal  
> Created at: 2024-07-13 21:46:45 UTC  
> Url: https://github.com/boostorg/mysql/issues/313#issuecomment-2227111504  

Anyway, thanks for pointing out your need - I need more users like you :)
