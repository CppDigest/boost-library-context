# #2706 - Adopt a websocket from an opened socket file descriptor [Closed]

> Username: shoedrip  
> Created at: 2023-07-23 19:47:00 UTC  
> Updated at: 2024-01-18 12:11:43 UTC  
> Closed at: 2024-01-18 12:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2706  

Hello,  
  
I have a use case where a primary server process handles initial websocket connections, and receive some data from a client then transfers the internal file descriptor socket to another process (along with whatever information from the initial upgrade request). I want to be able to pick that file descriptor from the sub-process and continue exchanging messages with the client transparently.  
  
I can easily create a socket from the native handle, then make a websocket::stream out of it, but I can't do any read/writes until the websocket is in an "open" state, which it can only transition to when calling .accept on it, which is bad because it always sends a 101 Switching Protocols payload when calling that method, even though from the client's perspective the websocket connection was already opened and messages were already exchanged.  
  
Basically I need a way to signal to boost.beast that the websocket::stream is already "opened", that the protocol has already switched, or the ability to do a "dry accept", accepting without sending any data to the client.  
Am I missing something?  
  
Thanks.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-07-24 03:25:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2706#issuecomment-1647142332  

I don't think this is officially supported.   
  
What you could try (but that's a hack) is open a connection, initialize it, and then switch out the descriptor of the subprocess. Do however note that the websocket negotiates terms of the connection, so you must be absolutely sure they're the same (which is why beast doesn't support this).  
  
This might also be a feature that could be added in the future if there are enough users. Your use case seems quite extra-ordinary.

---

## Comment 2

> Username: shoedrip  
> Created at: 2023-07-24 03:37:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2706#issuecomment-1647149703  

I see. I'm not sure about how to do the switcheroo after the websocket has been created though. I know I can call assign on a socket, but after the socket is moved to construct a websocket, I don't know how I can change the underlying fd again. Unless maybe doing some tricks with dup/dup2... I'll give it a go.  
  
I thought beast could handle it as I can easily do what I described in nodejs with the ws package, and it doesn't even attempt to be "low-level".

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-07-24 07:02:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2706#issuecomment-1647328148  

`.next_layer()` will give you the underlying asio object. It might be worth supporting, I'll look into it.

---

## Comment 4

> Username: ddevienne  
> Created at: 2023-07-24 08:23:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2706#issuecomment-1647443154  

FWIW, I think that's an interesting use-case, forking a separate process specifically to handle a given _session_, which was accepted on the main process, possibly with some initial back-and-forth (to determine for example whether to keep that connection _in-process_, or _fork-it_).  
  
If Boost.Beast gains support for this, I'd love a complete / worked example that demonstrates it. Preferably cross-platform of course.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-01-01 13:24:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2706#issuecomment-1873327356  

> FWIW, I think that's an interesting use-case, forking a separate process specifically to handle a given _session_, which was accepted on the main process, possibly with some initial back-and-forth (to determine for example whether to keep that connection _in-process_, or _fork-it_).  
  
@ddvienne, could you please elaborate on the use cases for this?
