# #1500 - Example Compile Issues [Closed]

> Username: xinthose  
> Created at: 2019-03-05 04:49:34 UTC  
> Updated at: 2019-03-05 04:56:22 UTC  
> Closed at: 2019-03-05 04:56:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1500  

OS: Ubuntu 18.10  
Websocket async server [example](https://github.com/boostorg/beast/blob/develop/example/websocket/server/async/websocket_server_async.cpp) will not compile.  These are the errors Visual Studio Code is displaying:  
![screenshot from 2019-03-04 22-40-29](https://user-images.githubusercontent.com/12835202/53781733-a0e49c80-3ecf-11e9-9626-5e84da749c15.png)  
I just downloaded and installed Boost v1.69.0.  Am I missing something?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-05 04:50:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1500#issuecomment-469536535  

Are you mixing the 1.70 beta example with the 1.69.0 boost? You can't do that. If you want to use Boost 1.69 then you have to use the Beast 1.69 examples:  
https://github.com/boostorg/beast/tree/320037a6bc6e70adb5ec715dc2bdfce9ed0ba41e/example

---

## Comment 2

> Username: xinthose  
> Created at: 2019-03-05 04:56:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1500#issuecomment-469537494  

@vinniefalco Bingo!  Thank you sir again.
