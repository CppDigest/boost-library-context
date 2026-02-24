# #488 - Examples issues: exceptions and localhost [Closed]

> Username: ngrodzitski  
> Created at: 2017-06-14 14:37:26 UTC  
> Updated at: 2017-06-19 17:51:29 UTC  
> Closed at: 2017-06-19 17:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/488  

1. Some examples are lacking of exception handling on level of main().  
Providing wrong args or using busy port throws, and there is no handling, so application terminates in a rude way.  
  
2. It is better to use localhost by default for http_server example, because on windows you get an alert (asking for access to network), when first running the app.  
  
3. It would be nice to have a simple hello-world server.  
  
Version of Beast:  
```  
git log -1  
commit 18c68ceec99697121ec442b35dac6c9587a9566a  
Author: Vinnie Falco <vinnie.falco@gmail.com>  
Date:   Tue Jun 13 08:52:22 2017 -0700  
  
    Set version to 56  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-17 15:35:50 UTC  
> Url: https://github.com/boostorg/beast/issues/488#issuecomment-309222093  

What do you think about the **server-framework** example?  
https://github.com/vinniefalco/Beast/tree/master/example/server-framework
