# #164 - Moving from a thread to cobalt: am I doing this right ? [Closed]

> Username: jcelerier  
> Created at: 2024-03-21 15:05:15 UTC  
> Updated at: 2024-05-30 16:59:16 UTC  
> Closed at: 2024-05-30 16:59:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/164  

Hello,  
  
I have an external API from which I receive messages from external threads - can be user-created threads with std::thread, or even things coming from specific internal OS event loops.  
  
I started prototyping with cobalt: this is what I got so far:  
  
```c++  
#include "utils.hpp"  
  
#include <libremidi/libremidi.hpp>  
  
#include <boost/asio/detached.hpp>  
#include <boost/cobalt.hpp>  
  
namespace asio = boost::asio;  
namespace cobalt = boost::cobalt;  
  
// Could go in a libremidi/cobalt.hpp helper  
namespace libremidi  
{  
struct channel  
{  
  cobalt::channel<libremidi::message>& impl;  
  
  // This can be called from any random thread  
  void operator()(const libremidi::message& message)  
  {  
    // Copy of the message through a cobalt::spawn invokation ?  
    cobalt::spawn(  
        // writing to the channel's executor  
        impl.get_executor(),   
  
        // the task  
        [&impl = impl, message]() -> cobalt::task<void> {   
           // another copy in the channel ?  
           co_await impl.write(message);   
        }(),  
  
        // saw this in the doc, is this the best ?  
        asio::detached  
    );  
  }  
};  
}  
  
cobalt::main co_main(int argc, char** argv)  
{  
  cobalt::channel<libremidi::message> channel_impl{64};  
  libremidi::channel channel{channel_impl};  
  libremidi::midi_in midiin{{.on_message = channel}};  
  midiin.open_port(*libremidi::midi1::in_default_port());  
  
  // read our messages from the main thread's async loop  
  for (;;)  
  {  
    auto msg = co_await channel_impl.read();  
    std::cerr << msg << "\n";  
  }  
  co_return 0;  
}  
```  
  
  
My questions are :   
  
1/ Is this the most efficient to do what I want ? basically getting a piece of data from $ANY_THREAD to the cobalt loop.   
2/ Is the way I'm doing it safe ? Or do i need some amount of synchronization  
3/ How can I reduce the amount of copies ? Is it safe to take a reference to the message instead of a copy in the cobalt::spawn invokation  
4/ What kind of synchronization is used currently ? I'd like to ensure that at no point, an OS-level mutex is ever used, e.g. we want to make sure we aren't going to get context switches if we can avoid them.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-03-21 15:23:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/164#issuecomment-2012620748  

That's not correct, because cobalt is not thread safe by design. You'll need to `asio::post` to the cobalt executor / event loop.  
  
You might want to consider using an `asio::experimental::concurrent_channel` instead or use `cobalt::spawn` to post onto the cobalt loop.

---

## Comment 2

> Username: jcelerier  
> Created at: 2024-03-21 15:26:25 UTC  
> Url: https://github.com/boostorg/cobalt/issues/164#issuecomment-2012633614  

interesting, I first tried with asio::post(channel.get_executor(), ...) but the channel never received the message. Need to investigate more. I will also check with concurrent_channel, thanks for the hint.  
  
Would that be more efficient than the current thread-safe queue I'm using ? https://github.com/cameron314/readerwriterqueue this one allows a few hundred million messages per second (https://max0x7ba.github.io/atomic_queue/html/benchmarks.html)

---

## Comment 3

> Username: Lyulf  
> Created at: 2024-03-22 07:43:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/164#issuecomment-2014519477  

Which part of the above code is is not thread-safe?  
According to my understanding `co_await channel_impl.read()` is done on a single thread and `libremidi::channel` uses `cobalt::spawn` to write messages on the same executor so I don't think there should be a problem.  
The only problem I see is a non-empty capture list in a coroutine lambda but that is unrelated to cobalt not being thread safe.

---

## Comment 4

> Username: jcelerier  
> Created at: 2024-03-22 19:17:39 UTC  
> Updated at: 2024-03-22 19:19:12 UTC  
> Url: https://github.com/boostorg/cobalt/issues/164#issuecomment-2015758104  

right, something like this should be safer:  
  
```C++  
namespace libremidi  
{  
struct channel  
{  
  cobalt::channel<libremidi::message>& impl;  
  void operator()(const libremidi::message& message)  
  {  
    cobalt::spawn(impl.get_executor(), task(message), asio::detached);  
  }  
  
  cobalt::task<void> task(libremidi::message message)   
  {   
    co_await impl.write(std::move(message));   
  }  
};  
}  
```  
  
it was my understanding too that cobalt::spawn was the primitive that allowed cross-thread synchronization, from this comment in the docs:   
  
> Nothing in this library, except for awaiting a [cobalt/thread.hpp](https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#thread) and [cobalt/spawn.hpp](https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#spawn), is thread-safe. If you need to transfer data across threads, you’ll need a thread-safe utility like [asio::concurrent_channel](https://www.boost.org/doc/libs/master/doc/html/boost_asio/reference/experimental__basic_concurrent_channel.html). You cannot share any cobalt primitives between threads, with the sole exception of being able to [spawn](https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#spawn) a [task](https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#task) onto another thread’s executor.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-05-02 00:50:34 UTC  
> Url: https://github.com/boostorg/cobalt/issues/164#issuecomment-2089351803  

It does, but channel still requires everything to happen on one thread. And since I don't know what the executor of `impl` is, I can't really say that this is save, because your original code read from the channel on the main executor. I.e. the channel can't be used to cross threads, all it's operation need to be on the same thread.

---

## Comment 6

> Username: jcelerier  
> Created at: 2024-05-02 22:42:44 UTC  
> Updated at: 2024-05-02 22:43:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/164#issuecomment-2091866729  

Yes, for the channel I understand, but the write to the channel is wrapped into a cobalt::spawn on the channel's executor (the main one). Is that safe ?

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2024-05-30 15:22:25 UTC  
> Url: https://github.com/boostorg/cobalt/issues/164#issuecomment-2139923890  

Should be safe, yes.

---

## Comment 8

> Username: jcelerier  
> Created at: 2024-05-30 16:59:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/164#issuecomment-2140269905  

great, thanks !
