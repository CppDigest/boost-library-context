# #1969 - chat multi example [Closed]

> Username: smalls12  
> Created at: 2020-05-31 15:28:12 UTC  
> Updated at: 2020-05-31 21:42:58 UTC  
> Closed at: 2020-05-31 20:01:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1969  

Hello,  
  
I'm just getting my head around the multi chat example ( https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi ).  
  
Just looking for some information on the lifetime of the websocket_session. I see that the session is stored under shared_state via `::on_accept()` method and that the destructor will remove in from shared_sate.  
  
My question is more so how the destructor is even called since the shared_state is only storing a raw pointer.  
  
I'm guessing it has something to do with `boost::enable_shared_from_this<websocket_session>`.  
  
Thanks :)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-31 15:30:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636486816  

Yes the lifetime of the object is extended by binding a shared pointer into the completion handler. This is all explained in my presentation: https://youtu.be/7FQwAjELMek?t=1489

---

## Comment 2

> Username: smalls12  
> Created at: 2020-05-31 17:46:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636504267  

Oh perfect, that was easy to understand.  
  
I guess my follow up is in regards to the multithreaded aspect.  
  
The command line offers a way to bring up more threads for `ioc::run()`, what does beast use these threads for?  
  
I ask since the only synchronization I can find occurs in shared_state. Trying to figure out if those threads can somehow stomp on each other.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-05-31 18:00:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636505971  

You're supposed to already understand Asio. Beast uses threads to call `io_context::run` for the same reason that all Asio programs do.

---

## Comment 4

> Username: smalls12  
> Created at: 2020-05-31 18:35:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636510324  

I'll back track here.  
  
I noticed initially that the shared_state object stored raw pointers.  
With the use of multiple threads I was wondering if there could be a case where a dangling pointer was left lying around and if the use of a weak_ptr would be preferred.  
  
Looking back now, even with multiple threads calling `::run()` each http session is still protected from concurrency with `::make_strand()`.  
  
The shared_state object is also protected using a mutex.  
  
Just working my way through the understanding of the example, looking for some clarification on my understanding.  
  
I appreciate your time and responses :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-05-31 18:45:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636511537  

Yes everything you say is resonating with me :) Okay, so yeah there's a pointer in there but if you look closely you will note that the pointer is inserted when the session is created, while holding a mutex. And the pointer is removed when the session is destroyed, again while holding the mutex. The pointer is always valid as long as it is in the list.  
  
Yes a `weak_ptr` is an interesting idea but it is not necessary, for the reason stated above. It is provable that the pointer is always valid. But `weak_ptr` has other problems. No hash function is defined for them! Thus they cannot be used in an unordered container.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-05-31 18:46:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636511678  

Implementing a container that points to objects which can asynchronously come and go is tricky (and I am very certain that this code is correct).

---

## Comment 7

> Username: smalls12  
> Created at: 2020-05-31 19:21:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636516106  

Ah didn't thoroughly look through the `::send()` function.  
You go to the effort making a weak_ptr before sending the message anyways.  
I suppose that was the main issue I was thinking about; could we send on a session right after it was closed.  
The weak_ptr there would then catch it.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-05-31 19:40:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636518605  

> The weak_ptr there would then catch it.  
  
Exactly. There is a window of time when the destructor of the session is invoked, but before the destructor fully executes, where the object is in the list. The rules for `shared_ptr` are such, that if we are in the destructor, then it is guaranteed that `weak_ptr::lock` will fail. Subtle.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-05-31 19:42:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636518813  

> You go to the effort making a weak_ptr before sending the message anyways  
  
Yes, for the reasons stated in the comments. I was actually thinking of an improvement:  
```  
thread_local std::vector<boost::weak_ptr<websocket_session>> v;  
v.clear();  
```  
  
I added `thread_local`.

---

## Comment 10

> Username: smalls12  
> Created at: 2020-05-31 19:53:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636520257  

> There is a window of time when the destructor of the session is invoked, but before the destructor fully executes, where the object is in the list.  
  
And this would only be an issue if one member in the chat was trying to send a message right when another member disconnected right?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2020-05-31 19:56:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636520580  

> And this would only be an issue if one member in the chat was trying to send a message right when another member disconnected right?  
  
Right, because we release the mutex before we iterate the list of weak pointers.

---

## Comment 12

> Username: smalls12  
> Created at: 2020-05-31 20:01:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636521248  

Okay thanks for humoring me :)

---

## Comment 13

> Username: vinniefalco  
> Created at: 2020-05-31 20:02:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636521427  

Keep an eye out here: https://github.com/vinniefalco/BeastLounge/

---

## Comment 14

> Username: smalls12  
> Created at: 2020-05-31 20:15:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636523011  

Okay, think I will try and build that.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2020-05-31 20:17:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636523298  

> Okay, think I will try and build that.  
  
It is still a work in progress, so it might not work, but it might provide ideas if you look at the sources. I am in the middle of a large refactor.

---

## Comment 16

> Username: smalls12  
> Created at: 2020-05-31 20:21:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636523721  

Another pair of eyes : )

---

## Comment 17

> Username: vinniefalco  
> Created at: 2020-05-31 21:42:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1969#issuecomment-636534439  

The _master_ branch should compile (I'm working in _develop_).
