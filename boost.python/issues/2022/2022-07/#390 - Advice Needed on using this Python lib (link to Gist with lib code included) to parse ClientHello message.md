# #390 - Advice Needed on using this Python lib (link to Gist with lib code included) to parse ClientHello message [Open]

> Username: DragonOsman  
> Created at: 2022-07-21 18:11:38 UTC  
> Updated at: 2022-07-21 19:18:26 UTC  
> Url: https://github.com/boostorg/python/issues/390  

Hi, everyone.  
  
I have a C++ I wrote using Boost.Beast that runs over an encrypted connection with SSL ([source code](https://github.com/DragonOsman/currency_converter)).    
  
In there, I'm getting an error about an unsupported protocol from ClientHello.  So I want to use [this](https://gist.github.com/dholth/b766f20cdab26cee082f3f58d7c015d7) Python lib I found on Gist to try and parse the ClientHello.  My intention is to try and parse the protocol IDs to see if the HTTP/2 ID is present.    
  
But of course, as the library is in Python, I should either use Boost.Python for this or try to write the code in C++ myself or ask someone else to do it.  But I also don't know for sure if the library includes support for parsing protocol IDs out of the box--the `parseAlpn` function `yields` a protocol which seems like it might be what I want, but it could also just be the TLS protocol version.  I need help on figuring this out and also maybe on porting the code to C++ so I can use it without needing Boost.Python hopefully (since Boost.Python would add additional complication to my app, and might not really be what I need either since I don't want to include the Python interpreter in my app).    
  
So yeah, what should I do?  Try to use Boost.Python?  Or try to have someone port the code to C++ first and then use it?  And does this library already do what I need or do I need to extend it?  That's what I need advice on.  Thanks.
