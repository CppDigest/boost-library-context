# #220 - Explicit ping, close by server, concurrent pong and write on client-side ? [Closed]

> Username: rberlich  
> Created at: 2017-01-07 01:35:12 UTC  
> Updated at: 2017-01-24 20:39:05 UTC  
> Closed at: 2017-01-24 20:39:05 UTC  
> Url: https://github.com/boostorg/beast/issues/220  

Hi there,  
  
ping/pong and close are listed (https://vinniefalco.github.io/beast/beast/ref/websocket__opcode.html) as valid opcodes, and I have a few questions about these:  
  
- Is my assumption correct that, under normal circumstances, I will never have to initiate ping/pong between client server manually, i.e. this happens automatically behind the scenes via beast::websocket::stream and I never see corresponding messages e.g. in a beast::websocket::stream<socket_type>::async_read ?  
  
- I DO need to manually initiate beast::websocket::stream<socket_type>::close on the client-side, but will I have to also answer it manually on the server side ? Or does this happen behind the scenes ?  
  
- Concurrent reads or concurrent writes to a beast::websocket::stream are not thread safe. My use case may involve very lengthy calculations on the client side, hence the ping/pong needs to continue during the calculation in order to keep the connection alive. The calculation will be handled by a thread-pool, but at the risk that the calculation-thread writes the result to the stream at the same time as the pong (both on the client side). However, if the pong happens automatically (as I assume), I cannot send the write() for the result and the pong through the same strand, right ?  
  
Thanks and apologies if these may be newbie questions. I did not find information about these topics in the documentation.  
  
Kind Regards,  
Beet

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-07 02:07:51 UTC  
> Url: https://github.com/boostorg/beast/issues/220#issuecomment-271055295  

Great questions!  
  
During read operations (`read`, `read_frame`, `async_read`, `async_read_frame`), Beast will handle any received control frames for you. A ping will be responded to with a pong, a pong will call your pong callback if set (see https://vinniefalco.github.io/beast/beast/ref/websocket__pong_callback.html), and upon receiving a close frame, a close frame will be sent back automatically then the connection closed.  
  
These behaviors are transparent to the caller with respect to writes. In other words, even though your call to `async_read` could, in theory, perform a write (for example, by sending back a pong), this behavior is coordinated with other pending I/O so that there is no conflict with respect to user-initiated writes.  
  
1. You do not need to send pongs, these will happen automatically. However, you need to send a ping if there has been no activity for a while. And to reset your timer you would want to install a "pong callback" (see above). Beast will not send pings automatically.  
  
2. You do need to call `close` on the client side, but you don't have to worry about it on the server. Beast will respond to incoming close frames by sending a close in response (unless it already sent one). After receiving a close frame, a future call to a read function will return `beast::websocket::error::closed` as the error.  
  
3. As with Boost.Asio sockets, no members of `beast::websocket::stream` are thread-safe. If you have multiple threads calling `run` on the `io_service` associated with the stream, you must ensure synchronization (usually using a `strand`). You can have at most one user initiated read and one user initiated write at the same time. Beast's automatic handling of incoming control frames do not count toward that limit of one read and one write simultaneously. The function `async_pong` is provided only for completeness, except for specialized applications you would not need to send a pong yourself, since Beast always responds to pings with a pong for you.  
  
This should be all explained here, please let me know if you have any other questions:  
https://vinniefalco.github.io/beast/beast/websocket/control.html  
  
Thanks!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-01-24 20:22:46 UTC  
> Url: https://github.com/boostorg/beast/issues/220#issuecomment-274927181  

Is this resolved?

---

## Comment 3

> Username: rberlich  
> Created at: 2017-01-24 20:38:17 UTC  
> Url: https://github.com/boostorg/beast/issues/220#issuecomment-274931136  

Hi Vinnie,  
  
I’d say so — I have worked around this now.  
  
Kind Regards,  
Beet  
  
> Am 24.01.2017 um 21:22 schrieb Vinnie Falco <notifications@github.com>:  
>   
> Is this resolved?  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/vinniefalco/Beast/issues/220#issuecomment-274927181>, or mute the thread <https://github.com/notifications/unsubscribe-auth/ASlWXwWeGhlGhL1KnHmR83DDx6RJqhS4ks5rVl2XgaJpZM4LdSS4>.  
>

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-01-24 20:39:05 UTC  
> Url: https://github.com/boostorg/beast/issues/220#issuecomment-274931347  

Glad to hear it! I'll close this issue for now. If you have another problem feel free to open a new issue! Thanks!
