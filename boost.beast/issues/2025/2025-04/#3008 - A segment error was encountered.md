# #3008 - A segment error was encountered [Closed]

> Username: zhiweiwo  
> Created at: 2025-04-29 02:23:59 UTC  
> Updated at: 2025-04-30 00:46:45 UTC  
> Closed at: 2025-04-30 00:46:45 UTC  
> Url: https://github.com/boostorg/beast/issues/3008  

```  
04-27 09:22:32.175 F/DEBUG   ( 4390): signal 11 (SIGSEGV), code 1 (SEGV_MAPERR), fault addr 0x000000000000002a  
04-27 09:22:32.175 F/DEBUG   ( 4390): Cause: null pointer dereference  
04-27 09:22:32.175 F/DEBUG   ( 4390):     x0  b4000077f2b53980  x1  000000000000002a  x2  000000000000012f  x3  0000007436f7dc28  
04-27 09:22:32.175 F/DEBUG   ( 4390):     x4  0000000000000159  x5  b4000077f2b53aaf  x6  0000000000000000  x7  b400007732c02870  
04-27 09:22:32.175 F/DEBUG   ( 4390):     x8  0000000000000000  x9  0000000000004400  x10 000000000000012f  x11 b4000077f2b53980  
04-27 09:22:32.175 F/DEBUG   ( 4390):     x12 0000000000004400  x13 00000000000000ce  x14 b4000077f2b53956  x15 0000000000000000  
04-27 09:22:32.175 F/DEBUG   ( 4390):     x16 00000073edd964e0  x17 00000078becf9a40  x18 00000073ed07a000  x19 000000000000002a  
04-27 09:22:32.175 F/DEBUG   ( 4390):     x20 000000000000012f  x21 000000000000012f  x22 000000000000012f  x23 b400007782ba3e50  
04-27 09:22:32.175 F/DEBUG   ( 4390):     x24 0000000000000000  x25 0000000000000002  x26 0000000000000001  x27 b400007742b326f8  
04-27 09:22:32.175 F/DEBUG   ( 4390):     x28 b400007742b32710  x29 0000007436f7dbf0  
04-27 09:22:32.175 F/DEBUG   ( 4390):     lr  00000073eda94b88  sp  0000007436f7dbc0  pc  00000078becf9b20  pst 00000000a0000000  
04-27 09:22:32.175 F/DEBUG   ( 4390): 2 total frames  
04-27 09:22:32.175 F/DEBUG   ( 4390): backtrace:  
04-27 09:22:32.175 F/DEBUG   ( 4390):       #00 pc 000000000005fb20  /apex/com.android.runtime/lib64/bionic/libc.so (__memcpy_aarch64_simd+224) (BuildId: a87908b48b368e6282bcc9f34bcfc28c)  
04-27 09:22:32.175 F/DEBUG   ( 4390):       #01 pc 0000000000222b84  /system/lib64/libczslasr.so (bio_write+260) (BuildId: e5dbb975c80cd7233ed14806381450ea942ffc57)  
```  
This is an error encountered in the Android system, pointing to bio_write，null pointer dereference

---

## Comment 1

> Username: zhiweiwo  
> Created at: 2025-04-29 02:26:49 UTC  
> Url: https://github.com/boostorg/beast/issues/3008#issuecomment-2837271013  

```  
	net::io_context ioc;  
	ssl::context ctx{ssl::context::tlsv12_client};  
	tcp::resolver resolver_ws{ioc};  
	std::unique_ptr<websocket::stream<beast::ssl_stream<beast::tcp_stream>>> ws;  
	  
	ws = std::make_unique<websocket::stream<beast::ssl_stream<beast::tcp_stream>>>(ioc, ctx);  
		  
		  
     void WebSocketClient::read_loop()  
     {  
        beast::flat_buffer buffer;  
        beast::error_code ec;  
        while (true)  
        {  
            if (ws)  
            {  
                std::size_t bytes_transferred = ws->read(buffer,ec);  
            }  
        }  
    }  
  
    void WebSocketClient::write_loop()  
    {  
        beast::flat_buffer buffer;  
        beast::error_code ec;  
		while (true)  
		{  
			std::vector<char> asrbuffer(2560);  
			if (ws)  
			{  
				ws->binary(true);  
				std::size_t bytes_transferred = ws->write(net::buffer(asrbuffer.data(),asrbuffer.size()),ec);  
			}  
		}  
    }  
  
```  
This is the abbreviated form of my code.  Reading and writing are carried out in two different threads, using synchronous reading and writing.  Is it because ssl stream is thread-unsafe that I shouldn't read and write in two threads

---

## Comment 2

> Username: ashtum  
> Created at: 2025-04-29 14:18:49 UTC  
> Url: https://github.com/boostorg/beast/issues/3008#issuecomment-2839113916  

You can't call `read` or `write` from two different threads without using a synchronization primitive (such as `std::mutex`) to ensure that only one operation makes progress at a time.
