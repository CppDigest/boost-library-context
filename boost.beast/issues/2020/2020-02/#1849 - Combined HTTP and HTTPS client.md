# #1849 - Combined HTTP and HTTPS client [Closed]

> Username: Tectu  
> Created at: 2020-02-18 09:57:25 UTC  
> Updated at: 2020-02-24 09:26:49 UTC  
> Closed at: 2020-02-24 09:26:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1849  

I'd like to write an HTTP client class on top of `boost.beast` which will provide the user with an interface such as `my_client::synchronous_request(...)`.  
For this, I've been studying the examples & documentation. The two HTTP client examples (SSL & non-SSL) examples have been very helpful for this.  
  
My client class should be able to deal with both SSL and non-SSL connections. To keep things simple, I am trying to figure out whether I can build a client based on the `example/http/client/sync-ssl` that will also be able to handle non-SSL connections.  
  
Is this something that `boost.beast` endorses or do I have to have two internal (private) `my_client::perform_synchronous_request()` functions - one that can handle SSL connections and one that handles non-SSL connections or is `boost.beast` able to handle non-SSL connections although SSL is setup/configured?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-02-18 10:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-587395151  

Hi @Tectu, you have a couple of options.  
  
* note that the `next_layer` of an ssl stream is a tcp stream, so you could have a conditional at each stream function initiation, for example:  
  
```  
if (ssl_enabled_)  
  read(stream, buf, request, ec);  
else  
  read(stream.next_layer(), buf, request, ec);       
```  
  
* Another option is to have a polymorphic interface to the code that handles transport-related code (read, write, shutdown) and have two models, one ssl and one not  
  
* another option is to write a SynchronousStream type which does one of the above internally. This is the approach I took in a recent project involving beast. However, Vinnie would argue that the polymorphic transport class is preferrable. I would post the example code, but I wrote a model of AsyncStream which is far more complicated and would probably confuse the issue.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-02-18 10:44:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-587397197  

@vinniefalco Would it be worth discussing whether we could provide a stream type which was upgradeable to ssl and downgradable to tcp?  
  
It may not be the most technically pleasing solution, but our users may appreciate the utility.

---

## Comment 3

> Username: Tectu  
> Created at: 2020-02-18 14:33:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-587488828  

@madmongo1 thank you for your quick response. I am fairly new to `boost.beast` so just to be sure that I understand correctly I created this diagram which is how I understand option 2 (the polymorphic class approach). Does this correspond to the intended solution?  
  
![image](https://user-images.githubusercontent.com/1385320/74745277-ef678400-5263-11ea-98d5-d9e44924bf28.png)

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-02-18 15:33:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-587520624  

@Tectu that looks reasonable

---

## Comment 5

> Username: Tectu  
> Created at: 2020-02-19 14:21:46 UTC  
> Updated at: 2020-02-19 15:15:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-588256826  

I'll close this issue once I have it actually working. I think I'm close but I am still running into an issue that is currently unclear to me where `http::read()` returns a `bad version` error code "in TLS mode": https://stackoverflow.com/questions/60302428/boost-beast-combined-http-and-https-client

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-02-19 16:05:21 UTC  
> Updated at: 2020-02-19 16:07:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-588297062  

> http::read() returns a bad version  
  
Sounds like you did not perform the TLS handshake, and Beast is trying to interpret the binary TLS handshake data from the peer as an HTTP message (which it isn't).  
  
There's no need to reinvent the wheel here, Beast already comes with a few examples of "flex" (i.e. TLS and Plain) support, see:  
https://github.com/boostorg/beast/tree/develop/example/advanced/server-flex

---

## Comment 7

> Username: Tectu  
> Created at: 2020-02-19 16:24:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-588308285  

> There's no need to reinvent the wheel here, Beast already comes with a few examples of "flex" (i.e. TLS and Plain) support, see:  
> https://github.com/boostorg/beast/tree/develop/example/advanced/server-flex  
  
I'm very much against reinventing the wheel - I didn't come across that example as first of all I was looking too much for client examples and also I wasn't aware of the 'flex' term :)  
  
The example looks comparably complex - could you boil it down to a list of "necessary things to take care of" so that I can reference those points to the example and then translate it to my own client code? That would be greatly appreciated.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-02-19 17:11:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-588333422  

The point of this example is that it uses the Curiously Recurring Template Pattern ("CRTP") so that one base class can work with either TLS or plain connections. The particular stream type is provided by the derived class. It looks complex because it supports both HTTP and WebSocket.

---

## Comment 9

> Username: Tectu  
> Created at: 2020-02-20 12:50:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-589005412  

I am looking at the flex examples and I see how CRTP is great here. In fact, the library to which this `boost.beast` based HTTP client should be added makes already heavy use of CRTP.  
  
I don't mind changing my design based on the examples but before that I'd like to understand why my current solution is not working - otherwise I don't learn anything.  
I am new to `boost.beast` and I'd greatly appreciate if you could give me a few pointers on what to look out for when trying to debug this. Even if it's just something like _"add this error handler callback to there in order to figure out why the handshake might have failed."_

---

## Comment 10

> Username: madmongo1  
> Created at: 2020-02-20 13:18:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-589018145  

Can you send me a link to a minimal computable example of your solution? Maybe I can offer some advice.

---

## Comment 11

> Username: Tectu  
> Created at: 2020-02-21 14:25:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-589674960  

Thank you for your efforts, I appreciate it a lot. I've zipped my minimal test case: https://files.simulton.com/s/GSFpnKAEctLyKbn

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-02-22 17:53:35 UTC  
> Updated at: 2020-02-22 17:53:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-589981489  

Hi @Tectu just saw this. Taking a look

---

## Comment 13

> Username: madmongo1  
> Created at: 2020-02-22 18:33:16 UTC  
> Updated at: 2020-02-22 20:39:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-589985186  

I have created a github repo and pushed the corrected code:  
  
https://github.com/test-scenarios/beast-issue-1849  
  
I have also provided an example of another way to present the transport class  
  
https://github.com/test-scenarios/beast-issue-1849/tree/another-way

---

## Comment 14

> Username: Tectu  
> Created at: 2020-02-24 09:26:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1849#issuecomment-590232100  

@madmongo1 thank you _very_ much for your help - I appreciate your efforts. I understand now what I did wrong and your further improved example is definitely of great help!
