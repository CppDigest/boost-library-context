# #945 - Optional SSL [Closed]

> Username: eirslett  
> Created at: 2017-12-22 19:17:19 UTC  
> Updated at: 2017-12-31 21:53:50 UTC  
> Closed at: 2017-12-31 21:53:50 UTC  
> Url: https://github.com/boostorg/beast/issues/945  

There are separate examples for plaintext and encrypted protocols, for example the websocket client.  
How could you write a program that can work both with and without SSL? Imagine you make a program with a boolean configuration parameter `secure` that can be set to `true` or `false`, or maybe derived from a parsed URL like `ws://...` or `wss://...`.  
  
It looks like the type of the websocket objects are different depending on ssl or not - would you have to duplicate a lot of code to make this work? Or write an abstraction layer on top of them?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-22 19:38:21 UTC  
> Updated at: 2017-12-22 19:38:50 UTC  
> Url: https://github.com/boostorg/beast/issues/945#issuecomment-353662677  

>would you have to duplicate a lot of code to make this work  
  
No, you just need to implement your logic as a class template or function template parameterized on the type of underlying stream (plain or SSL). This is demonstrated in the advanced-flex server example:  
  
http://www.boost.org/doc/libs/master/libs/beast/example/advanced/server-flex/advanced_server_flex.cpp  
  
If you have questions about this example feel free to ask them here

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-12-31 19:19:46 UTC  
> Url: https://github.com/boostorg/beast/issues/945#issuecomment-354619942  

Has this issue been resolved?

---

## Comment 3

> Username: eirslett  
> Created at: 2017-12-31 21:53:50 UTC  
> Url: https://github.com/boostorg/beast/issues/945#issuecomment-354625301  

Yes, thanks!
