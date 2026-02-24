# #1699 - Websocket transfers big data at half the speed of the asio stream [Closed]

> Username: qtcdxxyuc  
> Created at: 2019-09-11 03:20:34 UTC  
> Updated at: 2019-09-12 11:23:37 UTC  
> Closed at: 2019-09-12 01:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1699  

### Version of Beast  
  
```c  
BOOST_BEAST_VERSION 248  
```  
  
---  
  
### Steps necessary to reproduce the problem  
  
- The send data size is 1MB.  
- Only one client on each connection is sending a server to accept.  
- Each asynchronous operation has an exclusive `asio::io_context` and has single-threaded execution optimization turned on.  
- `ipc`mean`asio::local::stream_protocol`,tcp mean ` asio::ip::tcp`  
  
---  
  
|        test         |  speed  |  
| :-----------------: | :-----: |  
|         ipc         |  9GB/s  |  
|   tcp(127.0.0.1)    | 5.4GB/s |  
| ipc.websocket.sync  | 3.6GB/s |  
| ipc.websocket.async | 2.0GB/s |  
| tcp.websocket.sync  |  3GB/s  |  
| tcp.websocket.async |  3GB/s  |  
|       memcpy        | 10GB/s  |  
  
I tried replacing websocket.base_stream with `ipc`(not tcp).  
  
And also tested performance under tcp(127.0.0.1).  
  
And even if I use io_context_pool, performance will not be greatly improved.  
  
The speed of the above websocket test is the maximum under 8 threads. The asio.stream can reach the maximum speed under single thread.  
  
---  
  
I use `google.benchmark`.  
  
> benchmark_ws_tcp  
  
```c++  
namespace {  
  
using tcp_stream = boost::beast::basic_stream<boost::asio::ip::tcp>;  
  
static void simpleServer(unsigned short port,std::atomic_bool &loop) {  
    using namespace boost::asio;  
    using namespace boost::beast;  
    io_context ioc(1);  
    ip::tcp::acceptor ac(ioc,{ip::make_address_v4("127.0.0.1"),port});  
    try {  
        auto socket = ac.accept();  
        websocket::stream<tcp_stream> ws(std::move(socket));  
        ws.accept();  
        ws.binary(true);  
        flat_buffer buffer;  
        while (loop) {  
            ws.read(buffer);  
            buffer.clear();  
        }  
    } catch (std::exception &e) {  
    }  
}  
  
}//namespace  
  
  
static void BM_WSTcp(benchmark::State &state) {  
    using namespace boost::asio;  
    using namespace boost::beast;  
    unsigned short port = folly::Random::randDouble(50000,60000); //Randomly a port  
    auto data_size = state.range(0);  
    auto data = ecv::RandomString(data_size);  //Randomly fill the string  
    std::atomic_bool loop{true};  
    std::thread t([&loop,&port]() {  
        simpleServer(port,loop);  
    });  
    std::this_thread::sleep_for(std::chrono::seconds(1));//wait for server create  
    io_context ioc;  
    std::thread ioc_runner([&ioc]() {  
        ioc.get_executor().on_work_started();  
        ioc.run();  
    });  
  
    ip::tcp::resolver resolver(ioc);  
    auto resolve_result = resolver.resolve("127.0.0.1",std::to_string(port));  
    websocket::stream<tcp_stream> ws(ioc);  
    ws.next_layer().connect(resolve_result.begin(),resolve_result.end());  
    ws.next_layer().socket().set_option(socket_base::send_buffer_size(data_size*2));  
    ws.handshake("127.0.0.1", "/");  
    ws.binary(true);  
  
    auto buf = buffer(data);  
    for (auto _ : state) {  
        //Performance testing here  
        //Since the async_stream does not allow multiple writes at the same time,   
        //wait for the last write and then continue  
          
	    //ws.write(buf);  //sync  
        ws.async_write(buf,use_future).get();  //async  
    }  
    ws.close(websocket::close_code::normal);  
    loop = false;  
    ioc.get_executor().on_work_finished();  
    t.join();  
    ioc_runner.join();  
    state.SetBytesProcessed(state.iterations() * data_size);  
    state.SetItemsProcessed(state.iterations());  
}  
  
BENCHMARK(BM_WSTcp)->Range(512<< 10, 20 << 10 << 10)->ThreadRange(1,8)->UseRealTime();  
```  
  
> benchmark_ws_ipc  
  
```c++  
using ipc_stream = boost::beast::basic_stream<boost::asio::local::stream_protocol>;  
//Other than the connection, other similar  
```  
  
---  
  
## BenchmarkResult  
  
`thread` mean Simultaneous testing in a multi-threaded manner.  
  
| scheme | data  | thread | bytes_per_second | items_per_second |  
| :----: | :---: | :----: | :--------------: | :--------------: |  
|  TCP   | 512KB |   1    |     1.10GB/s     |     2.26K/s      |  
|        |       |   2    |     1.48GB/s     |     3.04K/s      |  
|        |       |   4    |     2.91GB/s     |     5.96K/s      |  
|        |       |   8    |     2.83GB/s     |     5.79K/s      |  
|  IPC   | 512KB |   1    |     1.28GB/s     |     2.62K/s      |  
|        |       |   2    |     2.34GB/s     |      4.8K/s      |  
|        |       |   4    |     2.21GB/s     |      4.5K/s      |  
|        |       |   8    |     2.12GB/s     |      4.3K/s      |  
  
Another interesting phenomenon is sync interface is much faster than async.  
  
sync interface: Increased total bandwidth when multi-threaded.  
  
sync interface: Bandwidth will increase but the limit is not as good as the sync interface.  
  
Their speed is half slower than the speed of the asio  stream.

---

## Comment 1

> Username: qtcdxxyuc  
> Created at: 2019-09-11 03:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530202948  

In addition, it is feasible to replace the underlying with the use of ipc. After all, it satisfies the async_stream concept.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-09-11 04:12:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530211764  

> Websocket transfers big data at half the speed of the asio stream  
  
Well... yeah, of course - this is normal. In the client mode, the WebSocket implementation has to apply a random 32-bit XOR mask to the payload of every frame. See:  
  
https://stackoverflow.com/questions/14174184/what-is-the-mask-in-a-websocket-frame  
  
> Another interesting phenomenon is sync interface is much faster than async.  
  
This is also normal. Synchronous Asio operations do less work, and therefore are faster. However, they do not scale to thousands of connections.  
  
If you want, you can tinker with the code that generates and applies the mask to see if you can get a performance increase, or to measure the overall burden of the mask when the WebSocket stream is operating in the client mode:  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/detail/mask.hpp  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/detail/mask.ipp

---

## Comment 3

> Username: qtcdxxyuc  
> Created at: 2019-09-11 06:57:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530249000  

```c  
BM_wsMask/524288/threads:1       20671 ns      20671 ns      33420 bytes_per_second=23.6213G/s items_per_second=48.3764k/s  
BM_wsMask/524288/threads:2       10519 ns      21038 ns      33316 bytes_per_second=23.2092G/s items_per_second=47.5324k/s  
BM_wsMask/524288/threads:4        5415 ns      21475 ns      32644 bytes_per_second=22.7374G/s items_per_second=46.5662k/s  
BM_wsMask/2097152/threads:1      84205 ns      84205 ns       8319 bytes_per_second=23.1949G/s items_per_second=11.8758k/s  
BM_wsMask/2097152/threads:2      45565 ns      91130 ns       7774 bytes_per_second=21.4322G/s items_per_second=10.9733k/s  
BM_wsMask/2097152/threads:4      56709 ns     222229 ns       3476 bytes_per_second=8.7888G/s items_per_second=4.49987k/s  
```  
  
This seems to be a performance bottleneck.  
  
Ipc speed is 10G / s, the mask does have a certain impact.

---

## Comment 4

> Username: qtcdxxyuc  
> Created at: 2019-09-11 08:31:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530279335  

In the websocket protocol:  
  
- client->server need MASK  
- server->client not need  
  
So the server should write faster streams.  
  
But after my tests, the speed at which the server writes data to the client is not greatly improved.  
  
Its speed is similar to client write.  
  
```c  
---------------------------------------------------------------------------------------------  
Benchmark                                      Time           CPU Iterations UserCounters...  
---------------------------------------------------------------------------------------------  
BM_WS_A_read_Ipc/real_time/threads:1     1149771 ns     886871 ns        571 bytes_per_second=829.447M/s items_per_second=869.738M/s  
BM_WS_A_read_Ipc/real_time/threads:2     1210775 ns     981504 ns        596 bytes_per_second=787.656M/s items_per_second=825.917M/s  
BM_WS_A_read_Ipc/real_time/threads:4     1049930 ns    1182896 ns        648 bytes_per_second=908.322M/s items_per_second=952.444M/s  
BM_WS_A_read_Ipc/real_time/threads:8     1038998 ns    1197824 ns        704 bytes_per_second=917.879M/s items_per_second=962.466M/s  
BM_WS_A_read_Ipc/real_time/threads:10    1030125 ns    1193252 ns        710 bytes_per_second=925.785M/s items_per_second=970.756M/s  
```

---

## Comment 5

> Username: qtcdxxyuc  
> Created at: 2019-09-11 09:04:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530291709  

I use perf analysis:  
  
- `read`  
  - 90%  `read_some`  
    - 22% `boost::asio::detail::reactive_socket_service_base::receive`  
    - 20% `boost::asio::detail::buffer_copy`  
    - 19% `boost::beast::websocket::stream::impl_type::parse_fh`  
    - 17% `boost::asio::detail::socket_ops::sync_recv`  
    - 3%   `boost::beast::static_buffer_base::prepare`  
    - 3%   `boost::beast::websocket::stream::impl_type::check_stop_now`

---

## Comment 6

> Username: qtcdxxyuc  
> Created at: 2019-09-11 09:14:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530295049  

This seems that websocket is not suitable for use on ipc.

---

## Comment 7

> Username: djarek  
> Created at: 2019-09-11 22:03:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530584097  

If your problem just requires communication between processes and you never need to contact remotes, the websocket protocol contains a bunch of overhead that you don't really e.g.:  
- pings/pongs aren't needed  
- masking on the client is redundant  
- if you use datagram local domain sockets you don't even need any framing in your protocol  
  
As for making the websocket implementation faster - we could probably try avoiding doing a copy of the frame payloads in reads, but that may come with a different set of trade-offs.

---

## Comment 8

> Username: qtcdxxyuc  
> Created at: 2019-09-12 01:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530626636  

In fact, for some reason I want to implement websocket on ipc, so the service can switch quickly on ipc or tcp. But in fact this doesn't seem very realistic.  
I will separate these 2 pieces of communication and let them run separately.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-09-12 01:29:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530626998  

> ... so the service can switch quickly on ipc or tcp. But in fact this doesn't seem very realistic.  
  
You can do that, simply by creating a thin layer on top of websocket and ipc that has a message-based API. For the IPC implementation it is easy enough to just prefix the message with a length, and then send the raw message payload - no need to fiddle with masks and compression.

---

## Comment 10

> Username: qtcdxxyuc  
> Created at: 2019-09-12 02:05:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530633992  

The `boost::beast::Websocket::stream` directly performs the data mask. Does it mean that I need to inherit and then override the mask part of this class.  
  
Can you tell me which piece I need to replace?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-09-12 11:13:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530778775  

No that's not what I mean. I'm saying, that you create a layer which provides a message-based API, and then you write two implementations for it. One which uses beast WebSocket and the other which uses IPC. For the IPC you do not use Beast at all you just write your own code, which will be trivial since you do not have to worry about masking, control frames, or compression.

---

## Comment 12

> Username: qtcdxxyuc  
> Created at: 2019-09-12 11:23:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1699#issuecomment-530781584  

Ok. I will implement a simple protocol on ipc.
