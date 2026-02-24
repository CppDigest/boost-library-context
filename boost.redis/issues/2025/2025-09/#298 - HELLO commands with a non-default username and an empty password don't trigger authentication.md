# #298 - HELLO commands with a non-default username and an empty password don't trigger authentication [Closed]

> Username: anarthal  
> Created at: 2025-09-01 17:54:23 UTC  
> Updated at: 2025-09-04 14:48:02 UTC  
> Closed at: 2025-09-04 14:48:02 UTC  
> Url: https://github.com/boostorg/redis/issues/298  

Currently, we don't send the AUTH part of the HELLO command if the password is empty. Using ACLs, it's possible to have a user with a custom username and an empty password (although probably unlikely). I'm adding more tests around this, so I can safely implement this.
