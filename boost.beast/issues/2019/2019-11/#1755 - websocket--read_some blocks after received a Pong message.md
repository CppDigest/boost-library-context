# #1755 - websocket::read_some blocks after received a Pong message [Closed]

> Username: eliot-exdev  
> Created at: 2019-11-04 15:02:46 UTC  
> Updated at: 2019-12-27 12:43:30 UTC  
> Closed at: 2019-12-27 11:48:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1755  

Hello,  
websocket::read_some blocks after reading a Pong message (control message) and  
waiting for the next frame.  
  
What I'am doing:  
I am using several client websockets. Before I do a read operation on a websocket  
I am using select to make sure that a read on a socket won't block.  
I want to manage my control message like Ping and Pong on my own because  
the protocol I am using requires specific payload.   
1. I'm sending a Ping to the server,  
2. call  select on the fd (there will be data because of the pong message from server)  
3. call read_some, my callback is called and afterwards read_some is blocked because it waits  
for the next frame after the Pong.  
  
Pseudo code  
```  
 ws = std::unique_ptr<websocket::stream<tcp::socket>>(new websocket::stream<tcp::socket>(*ioc));  
 ws->control_callback([this](const websocket::frame_type kind, const beast::string_view payload) {}  
ws->handshake(host, "/"); // connect  
ws->ping(websocket::ping_data(payload)); // send ping  
select(ws->next_layer().native_handle()) // to the select, there will be data, the pong  
ws->read_some(flatBuffer, buffer.getRemainingBytes()); // will call callback and blocks afterwards and waiting for more data  
```  
  
In my opinion it's not a good idea to handle the control messages like ping/pong within  
the read_some method. It would be better to return like we are reading a normal frame  
and let the user decide what to do. There is already a method is_message_done() (fin bit).  
Why not introduce  methods like is_message_ping() or is_message_pong() so that the user  
can check the type of the frame?  
  
I did a small workaround in my git repo:  
https://github.com/eliot-exdev/boost-beast.git  
branch ping_pong_fix.  
  
If a ping/pong is received the callback will be called and afterwards return with 0  
(nothing read). So the read_some won't block anymore a I have the needed payload  
and the information about ping/pong in my callback.  
  
To sum it up: think about if you really want to handle the ping/pong in a read request.  
If there is a any special payload needed for these messages the user cannot provide  
it at the moment. Perhaps the underlying protcol needs to consider special timings, ...  
It would be much better to return the payload to the user and give him  
a hint what the type of frame was received (fin bit, ping bit, pong bit, close might be a special case).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-04 15:03:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-549395062  

`websocket::stream` does not work in non-blocking mode

---

## Comment 2

> Username: eliot-exdev  
> Created at: 2019-11-05 12:00:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-549793658  

Well, what else should be used?  
It's ok when read_some  blocks if there no frame to read,that's I am checkling it before with select.  
But read_some blocks also of consuming a control frame.  
It also writes data to the socket in case that a ping is received (which is also not really  
the expected bevaviour of the caller of read_some)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-05 14:24:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-549844622  

Beast implements the websocket protocol as described in the RFC. Pings usually must be responded to with a pong with a prescribed payload, so Beast is compliant in this regard. If you want to send your own pings and pongs, you can do it with the `ping` or `async_ping` function. If you want to know when pings and pongs are received, set the **control callback** option.

---

## Comment 4

> Username: eliot-exdev  
> Created at: 2019-11-08 07:46:49 UTC  
> Updated at: 2019-11-08 13:00:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-551422257  

Well, if I have a look into the RFC (https://tools.ietf.org/html/rfc6455#section-5.5.3) there is written:  
  
> If an endpoint receives a Ping frame and has not yet sent Pong  
   frame(s) in response to previous Ping frame(s), the endpoint MAY  
   elect to send a Pong frame for only the most recently processed Ping  
   frame.  
  
So it should be possible to skip answering Ping control frames and just answer the very last one.  
There are good reasons for that: network load, cpu load, a more important message needs to be send. In the current implementation the user has no chance to skip an answer to a Ping request.  
  
For me, it's absolutely ok that read_some blocks until there is data to read.  
That's why I am testing the socket before with select to make sure that a read call won't block.  
But blocking read_some because of reading and answering a ping I did not expect because  
there was data.  
  
What's my goal at least: I having a couple of websockets opened to different servers.  
I put them all into a select and start reading from the websockets which have data  
(therefor i have exactly one read thread). This thread might currently block because of the   
handling of ping control frames.  
But I cannot create one thread per websocket in my use case.  
  
So what's the best way with beast to read with one thread from several websockets and make sure that a websocket won't block in a read if is there no data? I thought select should be the answer.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-11-08 16:41:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-551900686  

You can't be messing with `select`, that's a platform specific interface and Beast does not provide any guarantees that it will work.   
  
> what's the best way with beast to read with one thread from several websockets and make sure that a websocket won't block in a read if is there no data?  
  
Use the asynchronous APIs.

---

## Comment 6

> Username: eliot-exdev  
> Created at: 2019-11-19 16:47:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-555598436  

You can't use select in boost, but I can because boost gives me the native fd. I can do that in my  
code. That#s the way I can make sure that a read should not block. But when read_some will block  
just because it answers Ping requests inside, the select does not help me at all.  
  
As far as I understand it correctly, the async api just starts thread(s) (which will then block at least).  
Threads are not the answer to given problem (and not an option in my use case).

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-11-19 17:42:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-555623950  

> You can't use select in boost, but I can  
  
What I'm saying is that Beast does not provide any guarantees about how a program will behave if it calls `select` on sockets that are associated with HTTP or Websocket operations. You might be able to get it working (probably not) but there is no guarantee it will keep working between versions, or between different environments.

---

## Comment 8

> Username: eliot-exdev  
> Created at: 2019-11-19 17:55:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-555629126  

Hm, ok, I get your point.  
But how to realize multiplexing with Beast on multiple connections without and threads and  
busy polling?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-11-19 17:56:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-555629602  

You use asio in the idiomatic fashion, it performs fine. See the advanced-server example.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-11-20 11:50:44 UTC  
> Updated at: 2019-11-20 11:51:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-555970731  

Beast websocket is compliant with the RFC, and `websocket::stream` does follow the SRP - its single responsibility is to encapsulate the websocket protocol, which it does. There's nothing wrong with `read_some` also doing writes, because `stream::read_some` is a high level operation. It may surprise you to discover that `ssl::stream::read` can also perform writes (when the session key is renegotiated for example). Beast is written in 100% pure idiomatic asio, and has worked for hundreds of people in commercial and non-commercial settings. Lets review your question:  
  
> So what's the best way with beast to read with one thread from several websockets and make sure that a websocket won't block in a read if is there no data?  
  
Start with any of the multi-threaded examples. Such as: https://github.com/boostorg/beast/tree/develop/example/advanced/server  
  
Use idiomatic asio, have one thread call `io_context::run` and call asynchronous initiating function as usual. Do not use `select`.  
  
Have you used asio before?

---

## Comment 11

> Username: eliot-exdev  
> Created at: 2019-11-20 12:27:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-555982651  

Hi everybody,  
  
I am currently looking into the asio async methods. For the first tests it seems to be a solution like you expected (with the costs of a second thread, but this will be ok).  
I will report here when I finished the rework.  
  
Concerning automatic pong answering:  
Perhaps it would be possible to return bool value in the callback, so the user  
can decide if the ping should be answered? The user than can call pong method himself  
later.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2019-11-20 12:30:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-555983614  

> with the costs of a second thread  
  
You don't need a second thread, just call `io_context::run` from `main`. All the examples do this.

---

## Comment 13

> Username: eliot-exdev  
> Created at: 2019-11-22 06:17:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-557405988  

Hi Vinnie,  
  
I just tested the async api. It looks good for me.  
But asio is spawning an second thread internally although I am calling ioc.sun() directly  
from main (see gdb):  
  
```  
(gdb) info threads  
  Id   Target Id         Frame   
* 1    Thread 0x7ffff7fd6800 (LWP 5884) "main_websocketc" 0x00007ffff7345bb7 in epoll_wait (epfd=4, events=0x7fffffffd510, maxevents=128, timeout=-1) at ../sysdeps/unix/sysv/linux/epoll_wait.c:30  
  2    Thread 0x7ffff6e85700 (LWP 5888) "main_websocketc" 0x00007ffff7bc39f3 in futex_wait_cancelable (private=<optimized out>, expected=0, futex_word=0x5555558ff8e8)  
    at ../sysdeps/unix/sysv/linux/futex-internal.h:88  
  
```  
  
Besides that I looked into the asio code. It also uses select/poll to recognize  
if an socket is readable (see strace):  
  
```  
epoll_wait(4, [{EPOLLIN, {u32=536151604, u64=94266478364212}}], 128, -1) = 1  
timerfd_settime(5, 0, {it_interval={tv_sec=0, tv_nsec=0}, it_value={tv_sec=300, tv_nsec=0}}, {it_interval={tv_sec=0, tv_nsec=0}, it_value={tv_sec=0, tv_nsec=0}}) = 0  
epoll_wait(4,   
```  
  
Though my idea using select wasn't that bad.  
It's a pity that asio/beast is forcing the user to use the async api just to have non blocking  
reads.  
But in the end it's ok for me to have one more thread per ioc.  
  
Thank you for this great discussion and your work on boost::beast.  
I think it's great and it feels right in many ways compared to other libraries.  
  
regards  
André

---

## Comment 14

> Username: eliot-exdev  
> Created at: 2019-11-26 14:43:45 UTC  
> Updated at: 2019-11-27 07:29:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-558660926  

Hi all,  
so I have a new problem when using the async api.  
I want to reuse the http session for more than one request and running in this problem:  
boost_1.71.0/include/boost/beast/core/detail/stream_base.hpp:73: boost::beast::detail::stream_base::pending_guard::pending_guard(bool&): Assertion `! b_' failed.  
  
So that's the planned behavior because there should only one write/read per stream at once.  
  
Example code:  
```  
  
// original example is here:  
// https://www.boost.org/doc/libs/1_71_0/libs/beast/example/http/client/async/http_client_async.cpp//  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/strand.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
#include <thread>  
#include <condition_variable>  
#include <mutex>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const *what) {  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
class Request : public std::enable_shared_from_this<Request> {  
public:  
    Request(beast::tcp_stream &stream, std::string request, std::function<void(const char *, size_t)> f) : stream_(stream),  
                                                                                                           request_(request), f_(f) {}  
  
    void run(const std::string &host) {  
        auto self = shared_from_this();  
  
        req_.version(11);  
        req_.method(http::verb::get);  
        req_.set(http::field::host, host);  
        req_.set(http::field::user_agent, "myAgent");  
        req_.set(http::field::accept, "text/plain");  
        req_.keep_alive(true);  
  
        http::async_write(stream_, req_, [self](beast::error_code ec, size_t bytesTransferred) {  
            if (ec.failed()) {  
                fail(ec, "damn");  
                return;  
            }  
            http::async_read(self->stream_, self->buffer_, self->res_, [self](const beast::error_code ec, const size_t bytesTransferred) {  
                if (ec.failed()) {  
                    fail(ec, "damn");  
                    return;  
                }  
                self->f_(self->res_.body().c_str(), self->res_.body().length());  
            });  
        });  
    }  
  
private:  
    beast::tcp_stream &stream_;  
    std::string request_;  
    std::function<void(const char *, size_t)> f_;  
    beast::flat_buffer buffer_; // (Must persist between reads)  
    http::request<http::empty_body> req_;  
    http::response<http::string_body> res_;  
};  
  
  
// Performs an HTTP GET and prints the response  
class Session : public std::enable_shared_from_this<Session> {  
    std::string host_;  
    std::string port_;  
    int version_;  
    tcp::resolver resolver_;  
    beast::tcp_stream stream_;  
    beast::flat_buffer buffer_; // (Must persist between reads)  
    http::request<http::empty_body> req_;  
    http::response<http::string_body> res_;  
  
public:  
    // Objects are constructed with a strand to  
    // ensure that handlers do not execute concurrently.  
    explicit  
    Session(net::io_context &ioc, std::string host, std::string port, int version)  
            : host_(host), port_(port), version_(version), resolver_(net::make_strand(ioc)), stream_(net::make_strand(ioc)) {  
    }  
  
    // Start the asynchronous operation  
    void connect(std::function<void(bool)> f) {  
        // Set up an HTTP GET request message  
        req_.version(version_);  
        req_.method(http::verb::get);  
        req_.set(http::field::host, host_);  
        req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        // Look up the domain name  
        resolver_.async_resolve(  
                host_,  
                port_,  
                [this, f](beast::error_code ec, tcp::resolver::results_type results) {  
                    if (ec)  
                        return fail(ec, "resolve");  
  
                    // Set a timeout on the operation  
                    stream_.expires_after(std::chrono::seconds(30));  
  
                    // Make the connection on the IP address we get from a lookup  
                    stream_.async_connect(results,  
                                          [f](beast::error_code ec, tcp::resolver::results_type::endpoint_type) { f(ec.failed()); });  
                });  
    }  
  
    void sendGetRequest(std::string s, std::function<void(const char *, size_t)> f) {  
        std::make_shared<Request>(stream_, s, f)->run(host_);  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char **argv) {  
    auto const host = "www.google.de";  
    auto const port = "80";  
  
    // The io_context is required for all I/O  
    net::io_context ioc;  
  
    // create a session  
    auto session = std::make_shared<Session>(ioc, "www.google.de", "80", 11);  
  
    // run ioc in own thread  
    std::thread th([&ioc] {  
        auto work = boost::asio::make_work_guard(ioc);  
        ioc.run();  
    });  
  
    std::mutex m;  
    std::condition_variable cv;  
  
    // open the session  
    session->connect([&m, &cv](bool r) {  
        std::unique_lock<std::mutex> lock(m);  
        cv.notify_one();  
    });  
  
    // wait for connect  
    std::unique_lock<std::mutex> lock(m);  
    cv.wait(lock);  
  
    // send requests  
    for (int i = 0; i < 10; ++i) {  
        session->sendGetRequest("/", [](const char *data, size_t s) {  
            std::cout << "got data: " << std::string(data, s) << std::endl;  
        });  
    }  
  
    // wait key event  
    getchar();  
  
    // stop all  
    ioc.stop();  
    th.join();  
  
    return EXIT_SUCCESS;  
}  
  
```   
  
What's the best way to have several requests from different threads on one http session boost::beast?  
If need to queue it and make it thread safe than I can also use the sync api because there  
would be much lesser overhead. But than I have got the same problem again with the blocking  
read (remember: don't use select/poll).   
Currently I am running in circles because I cannot find a nifty way to use boost::beast to fit my problem.  
  
BTW using a mutex at sendGetRequest is not an option, because I want to use  
Pipelining (I don't want to wait with next write request before the answer is received).

---

## Comment 15

> Username: vinniefalco  
> Created at: 2019-11-26 16:36:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-558713134  

You already know the answer: implement a queue, and use the asynchronous APIs. The advanced examples already do this:  
https://github.com/boostorg/beast/blob/0b68ed651b6bc7b681cf440ed6a220089e21473f/example/advanced/server/advanced_server.cpp#L328

---

## Comment 16

> Username: eliot-exdev  
> Created at: 2019-11-27 07:28:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-558965496  

Perfect, the penny dropped :)  
Thanks for your patience and help!

---

## Comment 17

> Username: stale[bot]  
> Created at: 2019-12-27 07:55:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-569214974  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 18

> Username: eliot-exdev  
> Created at: 2019-12-27 11:48:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1755#issuecomment-569253794  

Yes, with async API everything works. The only issue that we need to accept that the user can't decide whether he wants to answer ping frames.
