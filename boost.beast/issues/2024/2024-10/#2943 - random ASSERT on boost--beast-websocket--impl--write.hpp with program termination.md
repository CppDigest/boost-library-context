# #2943 - random ASSERT on boost::beast:websocket::impl::write.hpp with program termination [Closed]

> Username: virgiliofornazin  
> Created at: 2024-10-23 17:09:36 UTC  
> Updated at: 2025-04-15 13:14:31 UTC  
> Closed at: 2025-04-15 13:14:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2943  

Got an exception using boost::beast websocket with SSL, on this part of code   
(file include/boost/beast/websocket/impl/write.hpp, line 549, on impl.wr_block.unlock(this), where wr_block.id_ == 0).  
  
    //--------------------------------------------------------------------------  
  
    upcall:  
        impl.wr_block.unlock(this); << THIS UNLOCKED RAISE THE ASSERTION  
        impl.op_close.maybe_invoke()  
            || impl.op_idle_ping.maybe_invoke()  
            || impl.op_rd.maybe_invoke()  
            || impl.op_ping.maybe_invoke();  
        this->complete(cont, ec, bytes_transferred_);  
    }  
}  
  
Using boost/1.86.0 from conan, built with clang++ using the following profile settings:  
  
[settings]  
arch=x86_64  
build_type=Debug  
compiler=clang  
compiler.cppstd=23  
compiler.version=18  
compiler.libcxx=libstdc++11  
os=Linux  
  
[conf]  
tools.build:compiler_executables = {"cpp": "/usr/bin/clang++", "c": "/usr/bin/clang"}  
tools.build:cflags=["-fsanitize=address", "-g", "-O0"]  
tools.build:cxxflags=["-fsanitize=address", "-g", "-O0"]  
tools.build:exelinkflags=["-fsanitize=address", "-g", "-O0"]  
tools.build:sharedlinkflags=["-fsanitize=address", "-g", "-O0"]  
tools.info.package_id:confs=["tools.build:cflags", "tools.build:cxxflags", "tools.build:exelinkflags", "tools.build:sharedlinkflags"]  
  
[options]  
b2/*:toolset=clang  
  
Maybe the assertion being raised probably because of releasing a lock already released?  
  
The call stack is attached as a file because cannot add it here since it´s big[callstack.txt](https://github.com/user-attachments/files/17495833/callstack.txt)

---

## Comment 1

> Username: ashtum  
> Created at: 2024-10-23 17:41:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2943#issuecomment-2432979687  

Is there a chance you're calling [websocket::stream::async_write](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write.html) before the first operation has finished?  
  
> The algorithm, known as a composed asynchronous operation, is implemented in terms of calls to the next layer's async_write_some function. The program must ensure that no other calls to [write](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/write.html), [write_some](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/write_some.html), [async_write](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write.html), or [async_write_some](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write_some.html) are performed until this operation completes.

---

## Comment 2

> Username: virgiliofornazin  
> Created at: 2024-10-23 19:11:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2943#issuecomment-2433228762  

it shouldn't because a strand is used to coordinate callbacks.  
i'll take a look at it.  
  
On Wed, Oct 23, 2024 at 2:41 PM Mohammad Nejati ***@***.***>  
wrote:  
  
> Is there a chance you're calling websocket::stream::async_write  
> <https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write.html>  
> before the first operation has finished?  
>  
> The algorithm, known as a composed asynchronous operation, is implemented  
> in terms of calls to the next layer's async_write_some function. The  
> program must ensure that no other calls to write  
> <https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/write.html>,  
> write_some  
> <https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/write_some.html>,  
> async_write  
> <https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write.html>,  
> or async_write_some  
> <https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write_some.html>  
> are performed until this operation completes.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2943#issuecomment-2432979687>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABDEMZT346JYJRPMYMTMQLTZ47NVNAVCNFSM6AAAAABQPJH5U6VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDIMZSHE3TSNRYG4>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: ashtum  
> Created at: 2024-10-23 19:32:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2943#issuecomment-2433271436  

well, an strand doesn't prevent you from calling `async_write` multiple times.

---

## Comment 4

> Username: virgiliofornazin  
> Created at: 2024-10-23 22:30:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2943#issuecomment-2433588959  

I know this, there´s a queueing mechanism to handle output from websocket  
client app, to allow one 1 async_write once a time.  
I'll put more debug info on this code, because it was done years ago and  
never got this assertion before.  
  
On Wed, Oct 23, 2024 at 4:32 PM Mohammad Nejati ***@***.***>  
wrote:  
  
> well, an strand doesn't prevent you from calling async_write multiple  
> times.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2943#issuecomment-2433271436>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABDEMZT7H7LWXTE4A3ZEITTZ472VLAVCNFSM6AAAAABQPJH5U6VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDIMZTGI3TCNBTGY>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 5

> Username: virgiliofornazin  
> Created at: 2024-10-25 17:10:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2943#issuecomment-2438366020  

I got the assert again, but what I detected: thread 1 create a ssl<tcp_stream> and perform a https request/reply (beast::async_write/beast::async_read), if I close the socket stream on another thread to abort the operation in case  
of a logical timeout I decide to wait, the assert is raised.  
  
I've managed to handle this connection status on my code, however, it seems to be a issue because the app couldn't  
abort on this assertion.  
  
Is there a way to 'close' the stream socket by another thread without raising this issue?

---

## Comment 6

> Username: ashtum  
> Created at: 2024-10-25 17:28:43 UTC  
> Updated at: 2024-10-27 18:25:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2943#issuecomment-2438406376  

If you want to close the stream from another thread, post the operation to the strand to prevent race conditions.   
```cpp  
asio::post(ws.get_executor(), [&]{ ws.async_close(...); });  
```  
Assuming the websocket stream was constructed using an strand.

---

## Comment 7

> Username: virgiliofornazin  
> Created at: 2024-10-27 18:12:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2943#issuecomment-2440123212  

yes. it was. I'm trying to handle a 'request timed out' or 'communication timed out issue' when you issue a beast::async_read and cannot get a answer in a specific amount of time.
