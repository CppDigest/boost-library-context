# #2156 - Issue with async incremental reading in Beast [Closed]

> Username: ErikSod  
> Created at: 2021-01-27 21:16:52 UTC  
> Updated at: 2022-01-09 05:17:12 UTC  
> Closed at: 2022-01-09 05:17:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2156  

I’ve been trying to implement an async version of the incremental reading sample (https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html) partially based on my understanding of an older stackoverflow post (https://stackoverflow.com/questions/48588627/asynchronous-processing-of-streaming-http-with-boostbeast) but I’ve hit some issues. When the incoming request has a body I get in a permanent loop of async_read_some’s callback getting error::need_buffer. I suspect I’m doing something wrong with the handling of the flat_buffer, although when I tried doing consume on the buffer during the async_read_header and async_read_some callbacks I instead starting getting partial_message errors. I’ve got a stripped down version of my class with just the critical parts below. I'm on version 1.73.   
  
`  
class StreamIncrementalReader final : public std::enable_shared_from_this<StreamIncrementalReader>  
{  
    boost::beast::flat_buffer _buffer;  
    boost::beast::tcp_stream _stream;  
    boost::optional<boost::beast::http::request_parser<boost::beast::http::buffer_body>> _parser;  
    boost::asio::io_context& _ioContext;  
  
    const static uint64_t MaxRequestBodySizeMB = 251 * 1024 * 1024;  
    const static uint64_t ReadBufferSizeMB = 1 * 1024 * 1024;  
      
    StreamIncrementalReader(  
        _In_ boost::asio::io_context& io_context,  
        _In_ boost::asio::ip::tcp::socket&& socket) :  
        _ioContext(io_context),  
        _stream(std::move(socket)),  
        _buffer(ReadBufferSizeMB) // Set the buffer size to 1 MB  
    {  
    }  
  
    void doAsyncRead()  
    {  
        _parser.emplace();  
        // This class just holds the currently parsed data.   
        _spCurrentRequestContext = nullptr;  
  
        _parser->body_limit(MaxRequestBodySize);  
        _stream.expires_after(std::chrono::seconds(30));  
  
        async_read_header(_stream, _buffer, *_parser,  
            boost::beast::bind_front_handler(&readHeaderHandler, this->shared_from_this()));  
    }  
  
    void SocketConnection::readHeaderHandler(boost::beast::error_code errorCode, std::size_t bytes_transferred)  
    {  
        if (errorCode)  
        {  
            return; // Error case  
        }  
        else if (!_parser->is_header_done())  
        {  
           return; // Error case  
        }  
  
        // This moves the headers to the context value, but doesn't consume the _buffer or touch _parser  
        _spCurrentRequestContext = CreateRequestContextFromBeastRequestHeaders(_parser->get());  
  
        if (_parser->is_done())  
        {  
            // Do another read, we're done with this one (no body to the request)  
            doAsyncRead();  
        }  
        else  
        {  
            // Question: Do I need to consume from the buffer or anything before doing read some?  
            async_read_some(_stream, _buffer, *_parser,  
                boost::beast::bind_front_handler(&readBodyHandler, this->shared_from_this()));  
        }  
    }  
  
    void SocketConnection::readBodyHandler(boost::beast::error_code errorCode, std::size_t bytes_transferred)  
    {  
        bool nextChunkNeeded = false;  
  
        if ((errorCode == beastHttp::error::need_buffer))  
        {  
            // Not done reading chunks, keep going.  
            // Note, for cases that the request has a body this is where I'm getting   
            // a permanent loop of this error code, since we do another async_read_some below when we get it.   
            errorCode = {};  
            nextChunkNeeded = true;  
        }  
  
        if (errorCode)  
        {  
            return;  
        }  
        else  
        {  
            if (_parser->is_done())  
            {  
                // Append these body bytes to the request context.  
                std::string body(static_cast<char*>(_parser->get().body().data), _parser->get().body().size);  
                _spCurrentRequestContext->spServerLayerRequest->requestBody += body;  
  
                // Handles the request in another class, not pictured here.   
                _transportServer.SendRequestToHandler(_spCurrentRequestContext);  
  
                // Wait for another request to come in, assume we’re done with this one.  
                doAsyncRead();  
            }  
            else if (nextChunkNeeded)  
            {  
                // Question: Do I need to consume on the _buffer here? Or not reuse the same _parser?  
                async_read_some(_stream, _buffer, *_parser,  
                    boost::beast::bind_front_handler(&readBodyHandler, this->shared_from_this()));  
            }  
            else  
            {               
                return; // log error and return here, shouldn't happen  
            }  
        }  
    }  
  
};  
`

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-27 21:23:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2156#issuecomment-768586601  

Hmm for a cursory glance this looks ok.  
Do you have some test data that is failing? I can run it through a debugger.

---

## Comment 2

> Username: ErikSod  
> Created at: 2021-01-27 22:26:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2156#issuecomment-768619913  

Sure, here's the buffer payload for one of the failed cases:   
  
X-SP-REQUESTRESOURCES: listUrl=%2Fteams%2FRequiredColumnForPics%2FLists%2FLocation%20Test  
Accept-Language: en-US  
Authorization: snip  
Content-Type: application/json;odata=verbose  
accept: application/json;odata=verbose  
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.141 Safari/537.36  
X-ClientService-ClientTag: SPList Web  
X-ServiceWorker-Strategy: CacheFirst  
Origin: https://microsoft.sharepoint-df.com  
Sec-Fetch-Site: cross-site  
Sec-Fetch-Mode: cors  
Sec-Fetch-Dest: empty  
Accept-Encoding: gzip, deflate, br  
  
{"parameters":{"__metadata":{"type":"SP.RenderListDataParameters"},"RenderOptions":5707271,"AllowMultipleValueFilterForTaxonomyFields":true,"AddRequiredFields":true}}

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-01-27 23:10:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2156#issuecomment-768638756  

OK thanks, I'll take a look at this in the morning.  
  
  
On Wed, 27 Jan 2021 at 23:27, ErikSod <notifications@github.com> wrote:  
  
> Sure, here's the buffer payload for one of the failed cases:  
>  
> X-SP-REQUESTRESOURCES:  
> listUrl=%2Fteams%2FRequiredColumnForPics%2FLists%2FLocation%20Test  
> Accept-Language: en-US  
> Authorization: snip  
> Content-Type: application/json;odata=verbose  
> accept: application/json;odata=verbose  
> User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36  
> (KHTML, like Gecko) Chrome/87.0.4280.141 Safari/537.36  
> X-ClientService-ClientTag: SPList Web  
> X-ServiceWorker-Strategy: CacheFirst  
> Origin: https://microsoft.sharepoint-df.com  
> Sec-Fetch-Site: cross-site  
> Sec-Fetch-Mode: cors  
> Sec-Fetch-Dest: empty  
> Accept-Encoding: gzip, deflate, br  
>  
>  
> {"parameters":{"__metadata":{"type":"SP.RenderListDataParameters"},"RenderOptions":5707271,"AllowMultipleValueFilterForTaxonomyFields":true,"AddRequiredFields":true}}  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2156#issuecomment-768619913>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSNA76UCZZLDXIIOJHLS4CHLPANCNFSM4WV6KAIA>  
> .  
>  
  
  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 4

> Username: ErikSod  
> Created at: 2021-01-28 01:57:16 UTC  
> Updated at: 2021-01-28 01:58:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2156#issuecomment-768741106  

Thanks! For what's it worth I've been debugging a bit and no matter how big I make the flat_buffer the outcome is the same here. It looks to only be reading part of the request. When I put a breakpoint right before the async_read_some in readHeaderHandler the buffer has the below data. No matter how many times I call async_read_some again in the readBodyHandler loop the buffer doesn't change, _parser doesn't have anything in its body value and _parser.is_done() == false. I'm not sure what the best way to debug what's in the _stream and why its not writing to the buffer (it should have the full data I posted above instead of this truncated version).   
  
**_buffer contents:**   
  
**begin_  ==** Authorization: foo  
Content-Type: application/json;odata=verbose  
accept: application/json;odata=verbose  
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.141 Safari/537.36  
X-ClientService-ClientTag: SPList Web  
X-ServiceWorker-Strategy: CacheFirst  
Origin: https://microsoft.sharepoint-df.com  
Sec-Fetch-Site: cross-site  
Sec-Fetch-Mode: cors  
Sec-Fetch-Dest: empty  
Accept-Encoding: gzip, deflate, br  
  
{"parameters":{"__metadata":{"type":"SP.RenderListDaýýýý  
  
**in_ ==** {\"parameters\":{\"__metadata\":{\"type\":\"SP.RenderListDaýýýý

---

## Comment 5

> Username: ErikSod  
> Created at: 2021-01-29 01:21:08 UTC  
> Updated at: 2021-01-29 01:21:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2156#issuecomment-769508070  

Just another quick update, if I change this:   
  
`boost::optional<boost::beast::http::request_parserboost::beast::http::buffer_body> _parser;`  
  
to this:   
  
`boost::optional<boost::beast::http::request_parserboost::beast::http::dynamic_body> _parser;`  
  
Everything appears to work as expected. So maybe a bug and\or user error in the use of buffer_body?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2156#issuecomment-850857891  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2156#issuecomment-1008232371  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
