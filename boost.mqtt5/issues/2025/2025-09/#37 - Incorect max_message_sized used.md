# #37 - Incorect max_message_sized used [Closed]

> Username: Stefan-Durstberger  
> Created at: 2025-09-19 11:30:52 UTC  
> Updated at: 2025-11-06 09:34:45 UTC  
> Closed at: 2025-11-06 09:34:45 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/37  

Hi all,  
  
I found a problem when publishing messages before the connection to the broker was successfully established. Its kind of a corner case, nevertheless, I wanted to report it.  
  
**Reproduction steps**  
  
1. Configure a max_message_size smaller than 256MB for the broker (i.e. 100KB)  
2. Instantiate the client  
3. Call `client::connect`  
4. ... connecting to the broker takes a bit, it has not been successfully established yet ...   
5. Publish a message which exceeds the broker's max_message_size  
7. ... Client connects to the broker and received the broker's "max_message_size" property  
9. Client publishes the message  
  
**Expected behaviour**  
The client reports an error "packet too large" and does not publish the message  
  
**Actual behaviour**  
The client publishes the message. The broker assumes a malfunctioning client and disconnects it.  
  
**Analysis**  
My guess, without looking to deep into your code:  
when calling `publish` before the connection was established, the default value for max_message_size (256MB) is captured in the queued publish call.  
After receiving the actual value from the broker, it is not updated in the queued publish call.  
  
  
**Possible follow-up problems**  
  
I did not check the following things yet:  
  
1. What happens if the client disconnects from the broker. Does it still use the already received "max_message_size" from the broker or does it fallback  to the default value.  
2. What happens if the client disconnects from the broker, and the broker is started with a smaller max_message_size. Does it update the max_packet_size in already queued publish calls.  
  
  
Best regards,  
  
Stefan

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-09-22 14:35:13 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/37#issuecomment-3319442218  

Thanks for the detailed report!  
  
You're right, we perform packet validation and serialization during the initial call to e.g. `async_publish`. Packets are not re-validated when we are re-sending them which leads to the issue you described.  
  
> *What happens if the client disconnects from the broker. Does it still use the already received "max_message_size" from the broker or does it fallback to the default value.*  
  
If the client disconnects via `async_disconnect`, then the limit is immediately reset to the default value. If broker initiates the disconnect, then the old limit remains until we connect again.  
  
> *What happens if the client disconnects from the broker, and the broker is started with a smaller max_message_size. Does it update the max_packet_size in already queued publish calls.*  
  
No, queued publishes aren't re-validated.  
  
We'll get this fixed soon, thanks again!
