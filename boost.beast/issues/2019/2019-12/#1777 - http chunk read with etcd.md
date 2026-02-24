# #1777 - http chunk read with etcd [Closed]

> Username: magicsupery  
> Created at: 2019-12-10 09:33:03 UTC  
> Updated at: 2020-02-22 04:40:56 UTC  
> Closed at: 2020-02-22 04:40:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1777  

beast version : 1.71.0  
  
i use http chunk to read watch info from etcd.  
===============================  
code example like this:  
`   void Session::onReadHeader(boost::system::error_code ec, size_t bytesTransferred)  
    {  
  
        SPDLOG_TRACE("{}", (void*)this);  
        assert(eventLoopSptr_->isInCurrentThread());  
        if(!ec)  
        {  
            assert(parser_.is_header_done());  
            // handle chunk  
            auto& res = parser_.get();  
  
            auto chunkStr = res["Transfer-Encoding"];  
  
            SPDLOG_TRACE("{} chunk check  {}", (void*)this, chunkStr.to_string());  
            if(chunkStr == "chunked")  
            {  
                chunk_ =  true;   
  
                auto self = shared_from_this();  
                auto chunkBodyCb = [=]  
                (uint64_t remain, boost::string_view body, beast::error_code& ec)  
                {  
                    SPDLOG_TRACE("{} self is {}", (void*)this, (void*)self.get());  
                    assert(self->eventLoopSptr_->isInCurrentThread());  
                    if(self->responseCallback_)  
                    {  
                        self->responseCallback_(0, true,   
                         self->parser_.get().base(),   
                         std::string(body.data(), body.size())  
                         );  
                    }  
  
                    return body.size();  
                };  
  
                myCallback_ = new std::function<  
                    size_t(uint64_t, boost::string_view, beast::error_code&)>(chunkBodyCb);  
  
                parser_.on_chunk_body(*myCallback_);  
            }  
  
            if(ssl_)  
            {  
                beast::http::async_read_some(  
                    sslStream_, readBuffer_, parser_,  
                    beast::bind_front_handler(&Session::onRead, shared_from_this()));  
            }  
            else  
            {  
                beast::http::async_read_some(stream_,   
                    readBuffer_, parser_,  
                    beast::bind_front_handler(&Session::onRead, shared_from_this()));  
            }  
        }  
        else  
        {  
            SPDLOG_ERROR("{} onReadHeader with error {}", (void*)this, ec.message());  
            if(responseCallback_)  
            {  
                responseCallback_(ec.value(), false, parser_.get().base(), parser_.get().body());  
            }  
        }  
    }  
  
    void Session::onRead(beast::error_code ec, size_t bytesTransferred)  
    {  
        SPDLOG_TRACE("{} bytesTransfered {}", (void*)this, bytesTransferred);  
        assert(eventLoopSptr_->isInCurrentThread());  
        if(!ec)  
        {  
            if(parser_.is_done())  
            {  
                SPDLOG_TRACE("{}", (void*)this);  
                SAFE_DELETE(myCallback_);  
                if(responseCallback_ && !chunk_)  
                {  
                    responseCallback_(ec.value(), true, parser_.get().base(), parser_.get().body());  
                }  
                return;  
            }  
            else  
            {  
  
                SPDLOG_TRACE("{}", (void*)this);  
                if(ssl_)  
                {  
                    beast::http::async_read_some(  
                        sslStream_, readBuffer_, parser_,  
                         beast::bind_front_handler(&Session::onRead, shared_from_this()));  
                }  
                else  
                {  
                    beast::http::async_read_some(  
                        stream_, readBuffer_, parser_,  
                         beast::bind_front_handler(&Session::onRead, shared_from_this()));  
                }  
            }  
        }  
        else  
        {  
            SPDLOG_ERROR("{} onRead with error {}", (void*)this, ec.message());  
            SAFE_DELETE(myCallback_);  
            if(responseCallback_)  
            {  
                responseCallback_(ec.value(), false, parser_.get().base(), parser_.get().body());  
            }  
        }  
    }`  
  
===========================  
there's two issue:  
  
1.when close the socket, the onRead function error_code is always 0 then the mode is chunk  
2.the chunk data from etcd(json data), will be devide into pieces, so everytime the responseCallback  
can not got the right json data, so how to make sure that all json data is received ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-16 02:25:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1777#issuecomment-565879341  

I'm not sure

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-01-15 02:57:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1777#issuecomment-574472700  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-01-15 13:36:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1777#issuecomment-574662593  

Can you provide a compiling test case?

---

## Comment 4

> Username: magicsupery  
> Created at: 2020-01-16 02:07:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1777#issuecomment-574947069  

> Can you provide a compiling test case?  
  
ok

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-02-15 02:42:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1777#issuecomment-586545668  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-02-22 04:40:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1777#issuecomment-589917877  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
