# #44 - Client hangs / receives empty messages after broker restart over TLS [Closed]

> Username: momoAmch  
> Created at: 2025-12-05 13:35:12 UTC  
> Updated at: 2025-12-10 09:24:59 UTC  
> Closed at: 2025-12-10 09:24:59 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/44  

Description:  
When using Async.MQTT5 with TLS, the C++ client works correctly when both the broker and client are started normally. However, if the client is already connected and the broker is restarted:  
  
The client begins receiving messages with empty topic and payload. And block every things   
publish_props and other properties are empty as well.  
No messages from other clients are actually being sent; the messages come directly from the broker and appear in the client’s async_receive loop.  
This issue does not occur over plain TCP, only over TLS.  
  
if i restart client again its works   
  
Steps to reproduce:  
  
Start the broker.  
Start the Async.MQTT5 TLS client and subscribe to a topic.  
Restart the broker while the client is still connected.  
Observe the client logs: messages appear with empty topic/payload.  
  
  
Im using nanmq brocker

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-12-08 16:21:05 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/44#issuecomment-3627813932  

I tested your scenario on the master branch and everything works correctly on my side.  
  
Did you check the `error_code` returned by `async_receive`? If it's `boost::mqtt5::client::error::session_expired` you'll need to re-subscribe, see the example here:  
https://github.com/boostorg/mqtt5/blob/6198b0e44a955563e49545564b2a1bf3dab58062/example/receiver.cpp#L93-L107  
  
Also, note that there was an issue affecting SSL streams that has been fixed in the upcoming Boost 1.90.0 (commit 3f50d39). Although if that's affecting you I would expect a different result than what you're describing.

---

## Comment 2

> Username: momoAmch  
> Created at: 2025-12-09 07:30:52 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/44#issuecomment-3630774465  

Hello, thank you for your time. The error returned by async_receive is **asio::error::operation_aborted** (operation canceled, ec.message). After this error occurs, subsequent messages have an empty payload and empty topic, and no further messages are received from other clients. If, upon this error, I perform an async_disconnect, then recreate the connection and re-subscribe, everything starts working again. But I believe there is an underlying issue, and this is not the ideal or correct way to handle it

---

## Comment 3

> Username: biljazovic  
> Created at: 2025-12-09 07:36:47 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/44#issuecomment-3630795837  

> The error returned by async_receive is **asio::error::operation_aborted** (operation canceled, ec.message).  
  
Thanks for the follow-up. The root cause is likely the same as in #38, which has been fixed in master (commit 3f50d39).

---

## Comment 4

> Username: momoAmch  
> Created at: 2025-12-09 09:07:47 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/44#issuecomment-3631134665  

thank you, I compiled the new version with the relevant fix, and it resolved my issue.
