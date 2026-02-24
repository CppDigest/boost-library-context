# #1637 - buffer_body issues on binary responses [Closed]

> Username: leobelen  
> Created at: 2019-06-14 03:39:45 UTC  
> Updated at: 2022-12-16 15:30:52 UTC  
> Closed at: 2019-07-21 23:07:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1637  

I am using the excellent beast api to implement a pseudo micro service but I am facing a problem that I don't see covered on the documentation or examples, as far as I can tell: how to use it to expose a binary object (namely a png image, produced by an OpenCv mat) using a buffer instead of an string or file body.   
  
So far, I have came up with the following code, based on the http sync server, however although it works, I have my doubts it is on the correct beast idiom. Developed on macOs, with Boost 1.70.0, on Safari, latest incarnations. I have tried to use vector_body for this but without success.   
  
Now, from this on, how can read the binary from a beast client side? Is there a proper way to do it? So far I have been experimenting directly with a browser, but the idea is to have both ends based on beast on a machine-to-machine system. In this case I was able to use vector_body, however, I am failing to get the std::vector after the response. How should I do that?  
  
Another thing that I am not seeing is who frees the memory allocated by the buffer...   
  
Server:   
``` cpp  
cv::Mat image(2000,4000, CV_8UC3, cv::Scalar(255,255,0));  
 std::vector<uchar> *imgBuf = new std::vector<uchar>();  
 cv::imencode(".png", image, *imgBuf);  
  
 http::response<http::buffer_body> res{http::status::ok, req.version()};  
 res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
 res.set(http::field::content_type, "image/png");  
 res.keep_alive(req.keep_alive());  
 res.body().data = imgBuf->data();  
 res.body().size = imgBuf->size();  
 res.prepare_payload();  
 return send(std::move(res));  
```  
  
Client:  
``` cpp  
cv::Mat outputImage;  
    try  
    {  
      boost::asio::io_context ioc;  
  
      // These objects perform our I/O  
      tcp::resolver m_resolver{ioc};  
      tcp::socket m_socket{ioc};  
  
      // Look up the domain name  
      auto const results = m_resolver.resolve(this->m_host, std::to_string(this->m_port));  
  
      // Make the connection on the IP address we get from a lookup  
      boost::asio::connect(m_socket, results.begin(), results.end());  
  
      // Set up an HTTP GET request message  
      http::request<http::string_body> req(http::verb::get, target.c_str(), this->m_version);  
      req.set(http::field::host, this->m_host);  
      req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
      // Send the HTTP request to the remote host  
      http::write(m_socket, req);  
  
      // This buffer is used for reading and must be persisted  
      boost::beast::flat_buffer buffer;  
  
      // Declare a container to hold the response  
      http::response<http::vector_body<uchar>> res;  
  
      // Receive the HTTP response  
      http::read(m_socket, buffer, res);  
  
      // Gracefully close the socket  
      boost::system::error_code ec;  
      m_socket.shutdown(tcp::socket::shutdown_both, ec);  
  
      // not_connected happens sometimes  
      // so don't bother reporting it.  
      //  
      if (ec && ec != boost::system::errc::not_connected)  
        throw boost::system::system_error{ec};  
  
      //.the next two are invalid statements!!!  
      std::vector<uchar> imgBuffer(res.body().data(), res.body().size());  
      outputImage = cv::imdecode(res.body().data(), cv::IMREAD_UNCHANGED);  
     
```  
Best in advance,   
Leo.

---

## Comment 1

> Username: djarek  
> Created at: 2019-06-14 04:55:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1637#issuecomment-501967497  

Try  
```  
cv::imdecode(res.body(), cv::IMREAD_UNCHANGED);  
```  
  
BTW: This is not related to Beast, seems more like a question about OpenCV...

---

## Comment 2

> Username: leobelen  
> Created at: 2019-06-14 21:00:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1637#issuecomment-502261710  

Well, in a sense the issue was with Beast... but yes, looking it at it is, you are right. Now, this worked like a charm. However, there is a issue with the server part. It prints a "write: need buffer" and I am almost sure that it is leaking memory. Now, I have tried to switch it to vector_body, but I dont find a way to move my vector to the message body... as res.body().data does not seem to be  assignable... do you know if there is there a way or documentation or example to look at?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-06-14 22:55:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1637#issuecomment-502304515  

```  
std::vector<char> v;  
...  
res.body() = v;  
```  
?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-07-14 23:03:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1637#issuecomment-511242504  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-07-21 23:07:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1637#issuecomment-513595394  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 6

> Username: badaix  
> Created at: 2021-09-05 19:39:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1637#issuecomment-913215586  

Just stumbled upon this issue, while I was searching for the "need_buffer" error, that I got when delivering a `jpg` using a `http::buffer_body`. For me it worked to add   
```  
res.body().more = false;  
```  
  
Hope that helps.

---

## Comment 7

> Username: liuziangexit  
> Created at: 2022-12-16 14:10:11 UTC  
> Updated at: 2022-12-16 15:30:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1637#issuecomment-1354905226  

Nice post, but what if i wanna to use boost::beast::http::async_write to send the response asynchronouslly?   
In that case, we need to put the data we are sending on heap, and free it after the send was done, how to do this?  
I didn't find any example code about this.  
  
My idea is, use shared_ptr(reference-counting) to contain the data, then have a lambda as the callback of boost::beast::http::async_write, which captures the shared_ptr with copy semantic. This means the lambda holds a reference to the data, and will unreference data once the lambda gets destructed(which will happen after async operation finished), something like this:  
  
  
```  
auto data_to_be_sent = std::make_shared<std::vector<uint8_t>>();  
  
do_something_to_fill_data();  
  
http::response<http::buffer_body> response;  
response.body().size = data_to_be_sent->size();  
response.body().data= data_to_be_sent->data();  
response.body().more=false;  
  
boost::beast::http::async_write(  
        stream,  
        response,  
        [response, data_to_be_sent /* This is where the reference happend */ ]  
        (boost::beast::error_code ec, std::size_t bytes_transferred) {  
          logger::debug("session", remote,  
                              " HTTP write ok");  
        });  
```  
  
To verify it actually works, I have a class like this that can show its lifetime:  
<img width="1420" alt="image" src="https://user-images.githubusercontent.com/15322450/208129998-9cf53fe7-483b-4314-a34a-697b835ed8db.png">  
  
And i just construct some of them, and make them to be captured by the callback  
  
  
```  
auto test1 = std::make_shared<life_time_test>();  
auto test2 = std::make_shared<life_time_test>();  
  
boost::beast::http::async_write(  
        stream,  
        response,  
        [response, test1, test2 /* This is where the copy happend */ ]  
        (boost::beast::error_code ec, std::size_t bytes_transferred) {  
          logger::debug("session", remote,  
                              " HTTP write ok");  
        });  
```  
  
And the log was like this:  
<img width="1027" alt="image" src="https://user-images.githubusercontent.com/15322450/208129728-e8129359-b84c-40c1-8e40-d7d5ef2a98b0.png">  
  
So this is how i do things now, any better ideas?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-12-16 15:19:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1637#issuecomment-1355049720  

can't you put it in the same place you put the `socket`?

---

## Comment 9

> Username: liuziangexit  
> Created at: 2022-12-16 15:30:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1637#issuecomment-1355080407  

> can't you put it in the same place you put the `socket`?  
  
ah yes, thats a easier solution
