# #3059 - High CPU usage [Closed]

> Username: ml232528  
> Created at: 2025-11-18 07:31:05 UTC  
> Updated at: 2025-11-21 02:46:45 UTC  
> Closed at: 2025-11-21 02:46:45 UTC  
> Url: https://github.com/boostorg/beast/issues/3059  

boost 1.89  
Using HTTP to receive a string of about 50MB, the Beast library consumes a lot of CPU.  
The following code is highly CPU-intensive:  
```  
    boost::beast::http::async_read(  
        *pItem->shStream,  
        *pItem->shBuffer,  
        *pItem->shParser,  
        [this, pItem](boost::beast::error_code ec, std::size_t bytes_transferred) {  
            this->OnRead(pItem, ec, bytes_transferred);  
        }     
    );  
  
```  
  
  
The complete code is as follows:  
```  
bool HttpClient::Start()  
{  
    bStart_ = true;  
  
	return true;  
}  
  
void HttpClient::Stop()  
{  
    bStart_ = false;  
    {  
        std::lock_guard<std::mutex> lock(mtxRequest_);  
        for (auto const &s : listRequest_)  
        {  
            s->shResolver->cancel();  
            s->shStream->close();  
        }  
        listRequest_.clear();  
    }  
}  
  
bool HttpClient::Post(std::string const& strRequest, std::string const& strTarget, std::string const& strContentType, ResponseCallback const& pFun)  
{     
    if (!bStart_)  
    {  
        LOG(ERROR) << "未开始";       
        return false;  
    }  
  
    std::shared_ptr<sRequestItem> pItem = std::make_shared<sRequestItem>();  
    pItem->shReq = std::make_shared<boost::beast::http::request<boost::beast::http::string_body>>();  
    pItem->shResolver = std::make_shared<boost::asio::ip::tcp::resolver>(CIoServerManage::GetInterface()->GetIoSer());  
    pItem->shStream = std::make_shared<boost::beast::tcp_stream>(CIoServerManage::GetInterface()->GetIoSer());  
    pItem->shRes = std::make_shared<boost::beast::http::response<boost::beast::http::string_body >>();  
    pItem->shParser = std::make_shared< boost::beast::http::response_parser<boost::beast::http::string_body>>();  
    pItem->shBuffer = std::make_shared<boost::beast::flat_buffer>();  
    std::weak_ptr<sRequestItem> wpItem = pItem;  // 弱引用  
    auto shThis = shared_from_this();  
    pItem->pFun = [this,pFun, wpItem, shThis](bool bSuccess, std::string const& strResponse) {  
            auto pItem = wpItem.lock();  
            if (!DeleleRequest(pItem))  
            {  
                return;  
            }   
            if (nullptr == pItem)  
            {  
                return;  
            }  
            if (pFun) pFun(bSuccess, strResponse);  
            //pItem->pFun = nullptr;  
        };  
  
  
    pItem->shReq->version(11);  
    pItem->shReq->method(boost::beast::http::verb::post);  
    pItem->shReq->target(strTarget); //http://192.168.116.216:18081/profile/coordinate_conversion 填“/profile/coordinate_conversion”  
    pItem->shReq->set(boost::beast::http::field::host, strHost_);  
    pItem->shReq->set(boost::beast::http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    pItem->shReq->set(boost::beast::http::field::content_type, strContentType);  
    //pItem->shReq->set(boost::beast::http::field::content_type, "application/json");  
    pItem->shReq->body() = strRequest;  
    pItem->shReq->prepare_payload();  
  
    {  
        std::lock_guard<std::mutex> lock(mtxRequest_);  
        if (listRequest_.size() >= nMaxRequest_)  
        {  
            LOG(ERROR) << "算法未回复太多";  
            return false;  
        }  
        listRequest_.push_back(pItem);  
    }  
  
    pItem->shResolver->async_resolve(  
        strHost_,  
        strPort_,  
        [this, pItem](boost::system::error_code ec, boost::asio::ip::tcp::resolver::results_type results) {  
            this->OnResolve(pItem, ec, results);  
        }  
    );  
  
    return true;  
  
            
}  
  
void HttpClient::OnResolve(std::shared_ptr<HttpClient::sRequestItem> const& pItem  
    , boost::system::error_code ec  
    , boost::asio::ip::tcp::resolver::results_type results)  
{  
    if (ec)  
    {  
#ifdef Platform_Windows  
        LOG(ERROR) << "resolve error:" << Convert::GBKtoUTF8(ec.message());  
#else  
        LOG(ERROR) << "resolve error:" << ec.message();  
#endif         
        pItem->pFun(false, "");  
        return;      
    }  
  
    if (!bStart_)  
    {  
        LOG(ERROR) << "已取消或停止" ;  
        pItem->pFun(false, "");  
        return;  
    }  
  
    // Set a timeout on the operation  
    pItem->shStream->expires_after(std::chrono::milliseconds(pItem->nTimeoutMillisecond_));  
  
    // Make the connection on the IP address we get from a lookup  
    pItem->shStream->async_connect(  
        results,  
        [this, pItem](boost::system::error_code ec, boost::asio::ip::tcp::endpoint const & ep) {  
            this->OnConnect(pItem, ec, ep);  
        }  
    );  
}  
  
void  HttpClient::OnConnect(std::shared_ptr<HttpClient::sRequestItem> const &pItem  
    , boost::beast::error_code ec  
    , boost::asio::ip::tcp::resolver::results_type::endpoint_type const &ep)  
{  
    if (ec)  
    {  
#ifdef Platform_Windows  
        LOG(ERROR) << "连接错误:" << Convert::GBKtoUTF8( ec.message());  
#else  
        LOG(ERROR) << "连接错误:" << ec.message();  
#endif  
         
        pItem->pFun(false, "");  
        return;  
    }  
  
    if (!bStart_)  
    {  
        LOG(ERROR) << "已取消或停止";  
        pItem->pFun(false, "");  
        return;  
    }  
  
    pItem->shStream->expires_after(std::chrono::milliseconds(pItem->nTimeoutMillisecond_));  
  
    // Send the HTTP request to the remote host  
      
    boost::beast::http::async_write(  
        *pItem->shStream,  
        *pItem->shReq,  
        [this, pItem](boost::beast::error_code ec, std::size_t bytes_transferred) {  
            this->OnWrite(pItem, ec, bytes_transferred);  
        }     
    );  
  
  
}  
  
void HttpClient::OnWrite(  
    std::shared_ptr<HttpClient::sRequestItem> const& pItem  
    , boost::beast::error_code ec  
    , std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
  
    if (ec)  
    {  
#ifdef Platform_Windows  
        LOG(ERROR) << "写入错误:" << Convert::GBKtoUTF8(ec.message());  
#else  
        LOG(ERROR) << "写入错误:" << ec.message();  
#endif        
        pItem->pFun(false, "");  
        return;  
    }  
  
    if (!bStart_)  
    {  
        LOG(ERROR) << "已取消或停止";  
        pItem->pFun(false, "");  
        return;  
    }  
  
    pItem->shStream->expires_after(std::chrono::milliseconds(pItem->nTimeoutMillisecond_));  
  
    pItem->shParser->body_limit(100 * 1024 * 1024); // 设置为100MB  
    //pItem->shParser->skip(true);  // 显式跳过 body 数据  
  
    boost::beast::http::async_read(  
        *pItem->shStream,  
        *pItem->shBuffer,  
        *pItem->shParser,  
        [this, pItem](boost::beast::error_code ec, std::size_t bytes_transferred) {  
            this->OnRead(pItem, ec, bytes_transferred);  
        }     
    );  
  
  
}  
  
  
void HttpClient::OnRead(  
    std::shared_ptr<HttpClient::sRequestItem> const& pItem  
    , boost::beast::error_code ec  
    , std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
  
    if (ec)  
    {  
#ifdef Platform_Windows  
        LOG(ERROR) << "读取错误:" << Convert::GBKtoUTF8(ec.message());  
#else  
        LOG(ERROR) << "读取错误:" << ec.message();  
#endif       
        pItem->pFun(false, "");  
        return;  
    }  
  
    if (!bStart_)  
    {  
        LOG(ERROR) << "已取消或停止";  
        pItem->pFun(false, "");  
        return;  
    }  
  
    if (!pItem->shParser->is_done())  
    {  
        LOG(ERROR) << "已取消或停止";  
        pItem->pFun(false, "");  
        return;  
    }  
  
    *pItem->shRes = pItem->shParser->get();  
  
    //std::string body_str = boost::beast::buffers_to_string(pItem->shRes->body().data());  
    std::string const & body_str = pItem->shRes->body();  
  
    pItem->pFun(pItem->shRes->result() == boost::beast::http::status::ok, body_str);  
  
  
}  
  
bool HttpClient::DeleleRequest(std::shared_ptr<sRequestItem> const &shItem)  
{  
    std::lock_guard<std::mutex> lock(mtxRequest_);  
    auto nSize =listRequest_.remove(shItem);  
    return (nSize > 0);  
}  
```  
  
Actually, I don't need to parse the body with beast, I just need the entire content of the body. I set pItem->shParser->skip(true); but I can't get the body content.

---

## Comment 1

> Username: ml232528  
> Created at: 2025-11-21 02:46:45 UTC  
> Url: https://github.com/boostorg/beast/issues/3059#issuecomment-3561094840  

After calling boost::beast::flat_buffer::prepare to set a buffer size slightly larger than the maximum data that needs to be received, the CPU usage rate is significantly reduced
