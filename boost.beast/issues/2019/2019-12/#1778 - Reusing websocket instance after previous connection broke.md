# #1778 - Reusing websocket instance after previous connection broke [Closed]

> Username: abhiarora4  
> Created at: 2019-12-11 13:49:33 UTC  
> Updated at: 2020-08-03 11:10:28 UTC  
> Closed at: 2020-01-19 00:44:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1778  

I am strugging to find a way to reuse the websocket instance after internet was abrupted. Even if I call   
  
  
            if (pWebStream_->is_open()) {  
                // if (pWebStream_->next_layer().next_layer().is_open());  
                pWebStream_->close(websocket::close_reason());  
  
                pWebStream_->next_layer().shutdown();  
                    pWebStream_->lowest_layer().close();  
                // pWebStream_->next_layer().shutdown;  
            }  
  
before reconnecting, it throws exceptions like "stream truncated" and "protocol is shutdown".   
  
I need to know how to close and create a new SSL session.  
  
Please help!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-11 14:40:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564574580  

Store the stream in a `boost::optional` or `std::optional` and re-construct it in place when you want to use it again.

---

## Comment 2

> Username: abhiarora4  
> Created at: 2019-12-11 14:52:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564580687  

I read your answer in another issue. How can I change the ssl stream of a websocket. Please help me with some code?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-12-11 14:52:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564580901  

You can't change the stream. Just destroy the object and create a new one?

---

## Comment 4

> Username: abhiarora4  
> Created at: 2019-12-11 14:54:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564581546  

Yes. I tried that. But it gave me seg fault or various "Invalid Read" message in valgrind. Is there anyway to gracefully delete the websocket instance? Because io_context is still looking for it even if it is deleted.

---

## Comment 5

> Username: abhiarora4  
> Created at: 2019-12-11 14:55:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564582201  

I was trying this to cleanup after a connection disconnect:  
  
            if (pIoc_) {  
                pIoc_->reset();  
                pIoc_->stop();  
                // pIoc_->run();  
            }  
            if (pWebStream_)  
                delete pWebStream_;  
            if (pingTimer_)  
                delete pingTimer_;

---

## Comment 6

> Username: abhiarora4  
> Created at: 2019-12-11 15:02:05 UTC  
> Updated at: 2019-12-11 15:02:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564585265  

Will it work?  
  
    boost::optional<boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>> socket;  
  
    // Before connecting!  
    socket.emplace(io_context);  
  
Please help. Is it what you are suggesting?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-12-11 15:30:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564597509  

> Is it what you are suggesting?  
  
Yes but you need the SSL context too, no?

---

## Comment 8

> Username: abhiarora4  
> Created at: 2019-12-11 15:39:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564601681  

Yes! Right. Thanks  
I am also mixing sync and sync operations in a single thread.   
Are these a problem?  
  
           // TODO: Send the Message from the vector  
            {  
                std::lock_guard<std::mutex> msgGaurd(msgMutex_);  
                while (!sendMsgQueue_.empty()) {  
                    const auto& m = sendMsgQueue_.front();  
                    std::cout << "Sending Message: " << m << std::endl;  
                    pWebStream_.value().write(boost::asio::buffer(m.toString()));  
                    sendMsgQueue_.pop_front();  
                    break;  
                }  
            }  
            std::cout << "Runing" << std::endl;  
            // Call io_context run_until to process read messages  
            pIoc_->run_until(std::chrono::steady_clock::now() +  std::chrono::milliseconds(DEF_IO_CONTEXT_RUN_FREQ_MS));  
            std::cout << "Here" << std::endl;  
            if (pWebStream_.value().is_open() and !isInactiveFor(DEF_INACT_TIMEOUT_SEC))  
                break;  
            // TODO: Check here for last activity timestamp  
            state_ = State::CLEANUP;  
            // Fall Though!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-12-11 15:47:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564604970  

> I am also mixing sync and sync operations in a single thread. Are these a problem?  
  
Maybe, depends on how you write the code. But generally, it is not a good idea to mix. Look at the websocket-chat-multi example for how to queue messages.

---

## Comment 10

> Username: abhiarora4  
> Created at: 2019-12-11 15:49:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564606034  

I am getting this:  
  
    : Assertion `ws_.rd_block_.is_locked(this)' failed.  
  
After it retires to connect.  
  
        case State::WEBSOCKET_HANDSHAKE:  
        {  
            Poco::URI uri(titanURI_);  
            std::string hostname = uri.getHost();  
            std::string port = std::to_string(uri.getPort());  
  
  
            // if (pWebStream_->is_open()) {  
            //     // if (pWebStream_->next_layer().next_layer().is_open());  
            //     pWebStream_->close(websocket::close_reason());  
  
            //     pWebStream_->next_layer().shutdown();  
            //         pWebStream_->lowest_layer().close();  
            //     // pWebStream_->next_layer().shutdown;  
            // }  
          
            ssl::context ssl_context{ssl::context::sslv23_client};  
            ssl_context.set_options(ssl::context::default_workarounds | ssl::context::no_sslv2 |  
                                ssl::context::no_sslv3);  
            ssl_context.set_verify_mode(ssl::verify_peer); // client side  
            ssl_context.load_verify_file(context_.caBundleFilePath);  
            ssl_context.use_certificate_file(context_.devCertiFilePath, ssl::context_base::pem);  
            ssl_context.use_private_key_file(context_.devKeyFilePath, ssl::context_base::pem);  
  
            // if (pIoc_ == nullptr)  
            // pIoc_ = new boost::asio::io_context;  
            // pWebStream_ = new websocket::stream<ssl::stream<tcp::socket>>{*pIoc_, ssl_context};  
            // if (pingTimer_ == nullptr)  
            // pingTimer_ = new boost::asio::steady_timer(*pIoc_);  
            pWebStream_.emplace(*pIoc_, ssl_context);  
  
            tcp::resolver resolver{*pIoc_};  
            const auto results = resolver.resolve(hostname, port);  
            std::cout << "TCP Connec" << std::endl;  
            boost::asio::connect(pWebStream_.value().next_layer().next_layer(), results.begin(), results.end());  
  
            std::cout << "SSL Connec" << std::endl;  
            pWebStream_.value().next_layer().handshake(ssl::stream_base::client);  
            pWebStream_.value().control_callback(std::bind(&AlarmNetSocket::onControlFrames, this, std::placeholders::_1,  
                                                                std::placeholders::_2));  
            std::cout << "Websocket Connec" << std::endl;  
            pWebStream_.value().handshake_ex(hostname, "/", std::bind(&AlarmNetSocket::append_headers, this, std::placeholders::_1));  
            pWebStream_.value().async_read(readBuf_, std::bind(&AlarmNetSocket::onRead, this,   
                            std::placeholders::_1, std::placeholders::_2));  
            pingTimer_->expires_after(std::chrono::seconds(5));  
            pingTimer_->async_wait(std::bind(&AlarmNetSocket::onPingTimer, this, std::placeholders::_1));  
            lastConnTimeStamp_ = std::chrono::steady_clock::now();  
            state_ = State::READY;  
        }

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-12-11 16:05:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-564613540  

Have you tried compiling and running the websocket examples? Do they work for you? If yes, then I suggest you start with the example which works and modify it slowly to suit your needs. I can't debug your entire program for you. And users of Beast are expected to know how to use asio. If you look at the source code where the assert is happening you should see a comment with some instructions.

---

## Comment 12

> Username: stale[bot]  
> Created at: 2020-01-10 16:48:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-573113869  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 13

> Username: stale[bot]  
> Created at: 2020-01-19 00:44:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-575952918  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 14

> Username: dgrr  
> Created at: 2020-08-02 10:10:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-667654545  

Hello,  
  
In case someone faces the same problem: Don't forget to reset the `ssl::context`. I got a lot of segfault for not resetting the ssl::context correctly. If someone has a problem reusing a `ssl::stream`, just try using `std::optional` for the `ssl::context` too.

---

## Comment 15

> Username: madmongo1  
> Created at: 2020-08-02 11:48:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-667664080  

@dgrr Generally, you will want only one `ssl::context` per program. Much like the `io_context`, I create it in `main()`, configure it, and pass a reference to it as a dependency for all ssl streams.  
  
If your program both makes outbound connections and accepts inbound connections, there is a strong case for two `ssl::context` per program.  
  
Creating more than one is an unnecessary burden.

---

## Comment 16

> Username: dgrr  
> Created at: 2020-08-02 18:42:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-667710052  

@madmongo1 right. So looks like I had another problem I magically solved. I was getting a segfault related with OpenSSL. I temporary fixed it by using a `std::optional` with the `ssl::context`. I really don't know why. I just removed the `optional` and I get no error.  
  
Thanks for clarifying.

---

## Comment 17

> Username: madmongo1  
> Created at: 2020-08-03 07:04:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-667845617  

@dgrr Good to hear that all is resolved.   
  
Are we in a position to close this issue now, or is there anything else I can help you with?

---

## Comment 18

> Username: dgrr  
> Created at: 2020-08-03 11:10:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1778#issuecomment-667962555  

@madmongo1 I just wanted to comment in case someone faces the same issue in the future. (this issue is already closed).  
  
Thanks!
