# #1852 - dispatch detect_session tcp_stream [Closed]

> Username: jpramosi  
> Created at: 2020-02-23 14:02:19 UTC  
> Updated at: 2020-03-18 00:48:39 UTC  
> Closed at: 2020-03-18 00:48:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1852  

Hello, is it required on a multithread server to start the dispatch in on 'beast::tcp_stream' in for example in 'detect_session' like this:  
  
```cpp  
void detect_session::run()  
{  
    boost::asio::dispatch(  
        m_stream.get_executor(), // <---- m_stream is 'beast::tcp_stream'  
        boost::beast::bind_front_handler(&detect_session::on_run,  
                                         this->shared_from_this()));  
}  
  
void detect_session::on_run()  
{  
    boost::beast::async_detect_ssl(  
        m_stream,  
        m_buffer,  
        boost::beast::bind_front_handler(&detect_session::on_detect,  
                                         this->shared_from_this()));  
}  
  
void detect_session::on_detect(boost::beast::error_code ec, bool result)  
{  
    if (ec)  
    {  
        LOG("detect_session::on_detect: " << ec.message());  
        return;  
    }  
    if (result)  
    {  
        std::make_shared<session<streams::ssl>>(std::move(m_stream),  
                                                         std::move(m_buffer),  
                                                         m_ssl_ctx,  
                                                         m_manager,  
                                                         m_server_timeout,  
                                                         m_session_cfg)  
            ->run();  
    }  
    else  
    {  
        std::make_shared<session<streams::plain>>(  
            std::move(m_stream),  
            std::move(m_buffer),  
            m_manager,  
            m_server_timeout,  
            m_session_cfg)  
            ->run();  
    }  
}  
```  
  
or just call 'on_run' directly and dispatch later in session when i got 'websocket::stream':  
  
```cpp  
    void session::run()  
    {  
            boost::asio::dispatch(  
                m_stream.get_executor(), // <---- m_stream is 'websocket::stream'  
                boost::beast::bind_front_handler(&session::on_run,  
                                                 this->shared_from_this()));  
    }  
```  
  
I do believe it is the second option but i am not quite sure.  
  
Thank you

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-02-23 14:08:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-590072479  

If multithreaded and sharing state between concurrent async operations then  You’ll want to dispatch initiating functions onto a strand.

---

## Comment 2

> Username: jpramosi  
> Created at: 2020-02-23 14:20:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-590073630  

Thanks for the response. Yes, a dispatch is required, no doubt.   
But should i start the dispatch already in:  
  
detect_session::run() on tcp_stream   
or later in  
session::run() on websocket:.stream  
  
i think i should have describe my question better :)

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-02-23 18:49:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-590102807  

@reapler I see what you mean.  
  
The question is whether the code you will/will not dispatch to the strand touch mutable objects.  
  
I think in your case there is no strict need to dispatch on_run(), but it's an almost zero-cost safety net. Remember that the dispatched code will be exectuted directly if you are already in the strand, or the first thread to enter it.

---

## Comment 4

> Username: djarek  
> Created at: 2020-02-24 20:42:22 UTC  
> Updated at: 2020-02-24 21:15:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-590538717  

I believe that you need to `dispatch` before initiating any ops that use `beast::tcp_stream` because it initiates 2 async operations consecutively, which could result in a data race if the 1 operation finishes (on another thread) before we return from initiating the first one.

---

## Comment 5

> Username: jpramosi  
> Created at: 2020-02-24 21:19:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-590553656  

I have studied the examples again and maybe [this](https://github.com/boostorg/beast/blob/9b10f08692611119a359c59f8b6892501601318a/example/advanced/server-flex/advanced_server_flex.cpp#L819) is relevant to the issue, since it also has no dispatch but dunno how it is handled internally.

---

## Comment 6

> Username: djarek  
> Created at: 2020-02-24 21:22:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-590555048  

Looks like a bug in the example.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-02-24 22:25:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-590580588  

All the examples should probably `dispatch` whether they need to or not, to be safe in any scenario where a wise user copy-pastes the code.

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-03-12 09:08:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-598080454  

I don't think the example has a bug. During detect_session::run, we are in the caller's strand, but we are also the only thread touching the stream.  
  
I'll update it to dispatch the implementation of run and provide a comment.

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-03-12 09:31:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-598090235  

...  
  
AsyncReadStream::get_executor() is not a thread-safe operation (see docs).  
  
Thus since the executor is stored in the stream, calling `stream_.get_executor()` on the stream in order to dispatch to the strand is no safer than calling `async_detect_ssl(stream_,...)`  
  
It seems to me that there is a design oversight in Asio, which makes it unsafe by default to use an io object as the storage for the only copy of the strand.

---

## Comment 10

> Username: madmongo1  
> Created at: 2020-03-12 09:42:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1852#issuecomment-598095010  

@djarek mentioned in chat that the dispatch is not to protect access to the stream, but to guard against beast::basic_stream initiating multiple operations internally on the wrong strand.  
  
If this is indeed a problem, it's a bug in beast::basic_stream.
