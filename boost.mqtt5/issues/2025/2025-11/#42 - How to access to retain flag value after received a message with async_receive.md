# #42 - How to access to retain flag value after received a message with async_receive ? [Open]

> Username: MelvinWarnet  
> Created at: 2025-11-04 10:55:20 UTC  
> Updated at: 2026-02-05 12:27:17 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/42  

Hello,  
  
I'm new to this library and to MQTT in general. But I search a lot and don't find any answer to my question.  
  
After receiving a message from my broker I want to check if this one was a retain message or not. I thought that it will exist a manner to access to the retain flag value of the message received but I didn't find anything like this.  
  
Please tell me if I have not be clear enough.

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-11-05 10:13:03 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/42#issuecomment-3490333063  

Hello,  
  
Unfortunately, we don't currently forward the retain flag. Adding support for this would likely require changing the `async_receive` completion signature.  
  
I'm also curious about your use case, since the retain flag is primarily meaningful to the broker.

---

## Comment 2

> Username: MelvinWarnet  
> Created at: 2025-11-05 15:58:51 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/42#issuecomment-3492038479  

Hello @biljazovic,  
  
Thanks for your reply, it's clear. I don't really have use case for now because I'm principally discovering the protocol and the lib. I was just wandering if it was possible. But I imagine that a client may process the information differently if it is a retain message.  
  
Have a nice day

---

## Comment 3

> Username: gizmocuz  
> Created at: 2026-02-03 17:12:22 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/42#issuecomment-3842575490  

I would like to see this issue to be re-opened if that is possible.  
  
There are various situations when a client application needs to know if the message has the retain flag set  
  
1. because it is just the protocol and each message has this flag  
2. an application wants to know it.  
  
Imagine my issue:  
- user has send a counter value with the retained flag on (1000)  
- but from that moment the user sends counter updates without the retained flag on (1001, 1002,2000, 3000, 4000)  
- the application keeps track of the counter value, because it can turn-over  
  
- the user restarts the application  
- the application received 1000 (the retained message!!!) ... checks the last value received 4000... ---> turn over?  
- the application receives a valid update next, 4001 .... ehhh  
  
What I do, if I receive 1000, I skip it one time if the retained flag is set ...   
  
So yeah, a user error! he should delete that retained value (which I will notify him too... but that's outside the scope of this issue)  
  
I think this class should hand back any variable that is in the technical specification  
  
Hope you will agree with me....
