# #3060 - extra data async_read - 1.85 [Open]

> Username: timprepscius  
> Created at: 2025-11-27 00:10:13 UTC  
> Updated at: 2025-11-27 20:30:31 UTC  
> Url: https://github.com/boostorg/beast/issues/3060  

Hey there:  
  
This is boost 1.85.0  
  
I'm doing this:  
  
```  
REPLICANT_HEADER_ONLY_INLINE  
void Stream::doRead()  
{  
	sLogDebug("replicant::relay::websocket::message", logOfThis(this));  
  
	if (!ws || !ws->is_open())  
		return;  
  
	auto weak_stream = weak_this(this);  
	auto read_buffer_ = this->read_buffer;  
  
	sLogDebug("replicant::relay::websocket::message", logOfThis(this) << logVar(read_buffer_->size()) << "before");  
  
	ws->async_read(  
		*read_buffer_,  
		[weak_stream, read_buffer_](beast::error_code ec, std::size_t bytes_transferred) {  
			if (auto stream = strong(weak_stream)) {  
				sLogDebug("replicant::relay::websocket::message", logVar(read_buffer_->size()) << logVar(bytes_transferred) << "after");  
			  
				stream->handleRead(ec, bytes_transferred);  
			}  
		}  
	);  
}  
```  
  
before read:  
read_buffer_->size() == 0  
  
after read (sometimes)  
read_buffer_->size() is 39, but bytes_transferred is 33  
the "correct" message is the last 33 bytes of the read_buffer_  
  
  
  
I've traced into:  
  
basic_stream.hpp:429  
```  
            this->complete_now(ec, bytes_transferred);  
```  
  
and it does seem that there is a buffer of 6, and then a buffer of 33  
in my tracing I see this:  
  
write.hpp:348  
```  
            // VFALCO What about consuming the buffer on error?  
            bytes_transferred_ +=  
                bytes_transferred - impl.wr_fb.size();  
```  
  
ec is:  
```  
(lldb) p ec  
(boost::beast::error_code) {  
   = {  
    d1_ = {  
      val_ = 0  
      cat_ = nullptr  
    }  
    d2_ = ""  
  }  
  lc_flags_ = 0  
}  
```  
  
I wonder if something needs to consume that 6 byte buffer.  
  
Anyhow, I'm using 1.85 which is a long time ago - has this been fixed? I tried to search a bit, couldn't find.  
  
Thanks!  
  
Tim

---

## Comment 1

> Username: timprepscius  
> Created at: 2025-11-27 00:20:17 UTC  
> Url: https://github.com/boostorg/beast/issues/3060#issuecomment-3583623188  

Wait, I might have found my problem, let me see before you spend any time on this.

---

## Comment 2

> Username: timprepscius  
> Created at: 2025-11-27 00:42:06 UTC  
> Url: https://github.com/boostorg/beast/issues/3060#issuecomment-3583659133  

No, I have no idea - it's always 6 bytes, right after accepting a connection -

---

## Comment 3

> Username: ashtum  
> Created at: 2025-11-27 05:15:26 UTC  
> Url: https://github.com/boostorg/beast/issues/3060#issuecomment-3584237547  

Could you please provide a minimal reproducible example that shows the issue?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2025-11-27 20:30:31 UTC  
> Url: https://github.com/boostorg/beast/issues/3060#issuecomment-3587207643  

If the incoming connection wants to negotiate TLS/SSL and you don't support that, well it could look like what is happening.
