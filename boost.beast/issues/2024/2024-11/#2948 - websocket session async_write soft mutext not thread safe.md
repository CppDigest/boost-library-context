# #2948 - websocket session async_write soft mutext not thread safe ? [Closed]

> Username: pa1gebro  
> Created at: 2024-11-10 18:42:00 UTC  
> Updated at: 2024-11-13 22:48:38 UTC  
> Closed at: 2024-11-13 22:48:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2948  

I have met this bug as below ,  show that, in the system there are more than one async_write operations for one websocket socket.  
  
![Image](https://github.com/user-attachments/assets/6db14d6c-3234-4903-87e1-c853954d7e13)  
  
And my code logic is same as the pesudocode as below, the websocket type is :   
  
```c++  
boost::beast::websocket::stream<boost::asio::local::stream_protocol::socket>  
```  
  
  
```c++  
int main() {  
    boost::asio::io_contxt io_context;  
    beasts::websocket<asio::local_stream::socket> websocket(io_context);  
    bool is_writing = false;  
  
    std::thread th1([&] {} {  
        for (auto i = 0; i < 10000; i++) {  
            boost::asio::post(io_context, [&]() {  
                enqueu_message(i);  
                signal_write() {  
                    if (is_writing) return;  
                    auto message = message_queue.front();  
                    websocket.async_write(  
                        buffer(message), [](const std::error_code& ec, std::size_t byte_tranfered) {  
                            message_queue.pop();  
                            if (message_queue.empty()) {  
                                is_writing = false;  
                                return;  
                            }  
                            // do async_write again for next message;  
                        });  
                }  
            });  
        }  
    });  
  
    std::thread th2([&] {} {  
        for (auto i = 0; i < 10000; i++) {  
            boost::asio::post(io_context, [&]() {  
                enqueu_message(i);  
                signal_write() {  
                    if (is_writing) return;  
                    auto message = message_queue.front();  
                    websocket.async_write(  
                        buffer(message), [](const std::error_code& ec, std::size_t byte_tranfered) {  
                            message_queue.pop();  
                            if (message_queue.empty()) {  
                                is_writing = false;  
                                return;  
                            }  
                            // do async_write again for next message;  
                        });  
                }  
            });  
        }  
    });  
}  
```  
  
As you can see, I use the io_context::post as the sync primitives to keep the thread safty for many threads, but the websocket connection just work in a single thread, so where could leak the double async_write bug ?

---

## Comment 1

> Username: sehe  
> Created at: 2024-11-11 01:54:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467080355  

I think the softmutex is only there to guard thread-safety, not to prevent overlapping writes. That's your job: https://live.boost.org/doc/libs/1_86_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write.html  
void enqueu_message(int) {  
    // push message to message_queue  
}  
  
> The algorithm, known as a composed asynchronous operation, is implemented in terms of calls to the next layer's async_write_some function. The program must ensure that no other calls to [write](https://live.boost.org/doc/libs/1_86_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/write.html), [write_some](https://live.boost.org/doc/libs/1_86_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/write_some.html), [async_write](https://live.boost.org/doc/libs/1_86_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write.html), or [async_write_some](https://live.boost.org/doc/libs/1_86_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write_some.html) are performed until this operation completes.  
  
Anyways, your code is very broken. Nothing even `run` the execution context, the program (if fixed enough to compile) will just terminate because the threads aren't joined.  
  
You post all `async_write` initiations without regards for when the previous one completed. Logically, since you *have* sequence writes, you cannot usefully multi-thread the write loop.  
  
Here's my sketch of how I'd typically layout the code instead:  
  
```c++  
#include <boost/beast.hpp>  
#include <boost/asio.hpp>  
#include <queue>  
namespace beast = boost::beast;  
namespace asio  = boost::asio;  
  
using Stream  = beast::websocket::stream<asio::local::stream_protocol::socket>;  
using Message = std::string;  
  
struct Demo : std::enable_shared_from_this<Demo> {  
    Demo(asio::any_io_executor ex) : websocket(ex, "demo.socket") {}  
  
    void enqueue(Message message) {  
        asio::post(websocket.get_executor(),  
                   [this, self = shared_from_this(), m = std::move(message)]() mutable {  
                       message_queue.push(std::move(m));  
                       if (message_queue.size() == 1)  
                           do_write_loop();  
                   });  
    }  
  
  private:  
    Stream              websocket;  
    std::queue<Message> message_queue;  
  
    void do_write_loop() {  
        if (message_queue.empty())  
            return;  
  
        websocket.async_write(asio::buffer(message_queue.front()),  
                              [this, self = shared_from_this()](std::error_code ec, size_t) {  
                                  if (!ec) {  
                                      message_queue.pop();  
                                      do_write_loop();  
                                  }  
                              });  
    }  
};  
  
int main() {  
    asio::io_context ioc;  
  
    auto demo = std::make_shared<Demo>(ioc.get_executor());  
    // auto demo = std::make_shared<Demo>(make_strand(ioc)); // strand for multithreaded ioc  
  
    auto worker = [demo] {  
        for (int i = 0; i < 10'000; i++)  
            demo->enqueue("Hello World " + std::to_string(i));  
    };  
  
    std::thread th1(worker), th2(worker);  
  
    ioc.run();  
  
    th1.join();  
    th2.join();  
}  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-11-11 04:03:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467187388  

the soft mutex allows 4 composed operations (read, write, ping, close) to happen concurrently, when the underlying stream can really only support 2 operations (read, write)

---

## Comment 3

> Username: pa1gebro  
> Created at: 2024-11-11 06:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467319081  

> the soft mutex allows 4 composed operations (read, write, ping, close) to happen concurrently, when the underlying stream can really only support 2 operations (read, write)  
  
I  have checked my code, and ensure there no any ping and pong , just read/write

---

## Comment 4

> Username: ashtum  
> Created at: 2024-11-11 06:13:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467324100  

> > the soft mutex allows 4 composed operations (read, write, ping, close) to happen concurrently, when the underlying stream can really only support 2 operations (read, write)  
>   
> I have checked my code, and ensure there no any ping and pong , just read/write  
  
You can have only one ongoing write operation at a time; you need to wait for it to complete before starting the next one. The same rule applies to read operations.

---

## Comment 5

> Username: pa1gebro  
> Created at: 2024-11-11 06:20:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467332477  

> > > the soft mutex allows 4 composed operations (read, write, ping, close) to happen concurrently, when the underlying stream can really only support 2 operations (read, write)  
> >   
> >   
> > I have checked my code, and ensure there no any ping and pong , just read/write  
>   
> You can have only one ongoing write operation at a time; you need to wait for it to complete before starting the next one. The same rule applies to read operations.  
  
Yep, in my code, there have two asynchronous agent both named read and write agent, and all the async_write and async_read is one by one as the document suggested

---

## Comment 6

> Username: ashtum  
> Created at: 2024-11-11 06:27:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467340497  

> Yep, in my code, there have two asynchronous agent both named read and write agent, and all the async_write and async_read is one by one as the document suggested  
  
Where are you setting the `is_writing` flag to true?

---

## Comment 7

> Username: pa1gebro  
> Created at: 2024-11-11 06:30:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467344044  

> > Yep, in my code, there have two asynchronous agent both named read and write agent, and all the async_write and async_read is one by one as the document suggested  
>   
> Where are you setting the `is_writing` flag to true?  
  
the first time  before I call async_write (frome sleep to active same as your demo code)

---

## Comment 8

> Username: ashtum  
> Created at: 2024-11-11 06:51:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467371129  

> the first time before I call async_write (frome sleep to active same as your demo code)  
  
Are you facing the same issue with the demo code? If not, I suggest adding more logs to your code and reviewing the sequence of operations.

---

## Comment 9

> Username: pa1gebro  
> Created at: 2024-11-11 07:52:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467457328  

> > the first time before I call async_write (frome sleep to active same as your demo code)  
>   
> Are you facing the same issue with the demo code? If not, I suggest adding more logs to your code and reviewing the sequence of operations.  
  
yep， I use this code will generate the same result id_ != T::id. I have refactored my system into your pattern

---

## Comment 10

> Username: ashtum  
> Created at: 2024-11-11 07:56:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467463909  

> yep， I use this code will generate the same result id_ != T::id. I have refactored my system into your pattern  
  
I didn’t quite follow, are you getting the error with the exact code from the example or in your modified version?

---

## Comment 11

> Username: pa1gebro  
> Created at: 2024-11-11 09:20:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467632562  

> > yep， I use this code will generate the same result id_ != T::id. I have refactored my system into your pattern  
>   
> I didn’t quite follow, are you getting the error with the exact code from the example or in your modified version?  
  
But in my code, there is a dubiously composed operations like this  
  
```c++  
  
void completion_handler(const std::error_code& ec, std::size_t) {  
   if (ec) {  
     socket.async_close()  
   }  
}  
  
```  
  
could this code fragement casue this error ?

---

## Comment 12

> Username: ashtum  
> Created at: 2024-11-11 09:28:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2467648572  

> But in my code, there is a dubiously composed operations like this  
>   
> void completion_handler(const std::error_code& ec, std::size_t) {  
>    if (ec) {  
>      socket.async_close()  
>    }  
> }  
> could this code fragement casue this error ?  
  
You can't call `async_close` while a write operation is in progress, and, like with other operations, you need to post the operation to the io_context to avoid race condition.

---

## Comment 13

> Username: pa1gebro  
> Created at: 2024-11-11 12:32:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2468071398  

> > But in my code, there is a dubiously composed operations like this  
> > void completion_handler(const std::error_code& ec, std::size_t) {  
> > if (ec) {  
> > socket.async_close()  
> > }  
> > }  
> > could this code fragement casue this error ?  
>   
> You can't call `async_close` while a write operation is in progress, and, like with other operations, you need to post the operation to the io_context to avoid race condition.  
  
But I am confused why this composed method cause unlock error not lock error

---

## Comment 14

> Username: ashtum  
> Created at: 2024-11-11 12:55:04 UTC  
> Updated at: 2024-11-11 12:55:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2468114817  

I believe this's because you have a race condition in your code due to calling `async_close` from a parallel thread. Note that operations on `soft_mutex` are not thread-safe.

---

## Comment 15

> Username: pa1gebro  
> Created at: 2024-11-11 15:50:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2948#issuecomment-2468492863  

> I believe this's because you have a race condition in your code due to calling `async_close` from a parallel thread. Note that operations on `soft_mutex` are not thread-safe.  
  
The supernatural thing is that in the system evenry coucurrent operations will join into the asio::post call
