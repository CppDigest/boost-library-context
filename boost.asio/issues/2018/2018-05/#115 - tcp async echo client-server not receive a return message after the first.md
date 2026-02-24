# #115 - tcp async echo client/server not receive a return message after the first [Closed]

> Username: jenokizm  
> Created at: 2018-05-21 19:49:07 UTC  
> Updated at: 2020-12-30 00:51:34 UTC  
> Closed at: 2020-12-30 00:51:33 UTC  
> Url: https://github.com/boostorg/asio/issues/115  

I can't understand why. Is this my problem or the boost problem? Please help  
Code for test https://gist.github.com/jenokizm/a019930330e429754a1a5d1f860d0175  
Steps:  
1. Start the server  
2. To start the client  
3. Write messages to the Server. The server receives the messages and sends them back. But the problem is that they do not reach the client. Thus, a one-way relationship is obtained(

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:51:32 UTC  
> Url: https://github.com/boostorg/asio/issues/115#issuecomment-752289561  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#619](https://github.com/chriskohlhoff/asio/issues/619).
