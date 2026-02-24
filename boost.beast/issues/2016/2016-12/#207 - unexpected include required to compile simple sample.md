# #207 - unexpected include required to compile simple sample [Closed]

> Username: AGaliuzov  
> Created at: 2016-12-14 21:44:34 UTC  
> Updated at: 2016-12-16 15:59:21 UTC  
> Closed at: 2016-12-16 15:59:21 UTC  
> Url: https://github.com/boostorg/beast/issues/207  

I have tried to create the WebSocket server using the Beast. As it mentioned in the README file Beast is header only library I have put only `Beast/include` directory to my project space.  
During compilation I have received the following error  
  
`Beast/include/beast/websocket/detail/utf8_checker.hpp:17:39: fatal error: beast/unit_test/dstream.hpp: No such file or directory  
`  
To me it looks quite unexpectedly. Why `real` code includes something from unit tests.  
  
@vinniefalco could you please explain this or share the link to some docs?  
Do I need ass unit test to my project as well?  
  
P.S. I am using `master` branch. Looks like on the `develop ` branch  it works fine

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-12-15 01:22:50 UTC  
> Url: https://github.com/boostorg/beast/issues/207#issuecomment-267209201  

Yep, I see that. Those includes should not be there, I will try to fix it shortly.
