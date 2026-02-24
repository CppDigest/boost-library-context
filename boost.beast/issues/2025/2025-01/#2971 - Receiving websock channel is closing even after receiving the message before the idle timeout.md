# #2971 - Receiving websock channel is closing even after receiving the message before the idle timeout. [Closed]

> Username: shivank1111  
> Created at: 2025-01-15 05:48:41 UTC  
> Updated at: 2025-02-09 15:01:49 UTC  
> Closed at: 2025-02-09 15:01:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2971  

Boost Version: 354  
  
When opening two WebSocket channels, for example, ws:/../channel1/ and ws:/../channel2/, I have made changes to enable communication between the two channels.  
  
I have set the timeout to 30 seconds using the following code:  
  
beast::websocket::stream_base::timeout opt{  
    std::chrono::seconds(30),  
    std::chrono::seconds(30),  
    false  
};  
ws_.set_option(opt);  
  
However, the issue is that say both channels are opened at the same time. If, after 15 seconds, communication occurs between the two channels (e.g., from channel1 to channel2), the timeout for channel1 will restart and close the connection after 45 seconds(15+30 =45;from beginning). But for channel2, even though it is receiving messages, its timeout does not reset and will close exactly after 30 seconds, which I don't want.  
  
Is there any way we can reset the timer of the channel which is receiving.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-01-15 08:38:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2971#issuecomment-2591945563  

> But for channel2, even though it is receiving messages, its timeout does not reset and will close exactly after 30 seconds, which I don't want.  
  
If I understood correctly, you mean the connection closes even if it received a message less than 30 seconds ago? I can't reproduce this, receiving a message resets the timer. could you please provide an example?

---

## Comment 2

> Username: shivank1111  
> Created at: 2025-01-16 06:06:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2971#issuecomment-2594607943  

Hi @ashtum, I am using the base code from advanced_server_flex.cpp and the chat-multi code for communication between channels. I have made some changes, and I believe I might have replaced a part of the code responsible for resetting the timer whenever a channel receives a message.  
I suspect the changes might need to be made in the on_read function, but I am not entirely sure.  
  
  
  
Example:  
  
Let's say there are two channels: ws:/../channel1/ and ws:/../channel2/. Communication happens between these two channels. The channel sending the message is resetting the idle timeout as expected, but the channel receiving the message is not resetting the idle timeout.  
It would be very helpful if you could let me know which part of the code I should check to ensure the receiving channel can reset the idle timeout.
