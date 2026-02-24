# #1740 - mjpeg stream [Closed]

> Username: rakhecha42  
> Created at: 2019-10-22 06:47:58 UTC  
> Updated at: 2022-01-11 17:11:52 UTC  
> Closed at: 2019-10-24 07:09:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1740  

Hello. I have written the following code to simply display each frame of video on a local server. The frame changes each time I refresh, i.e. send a new request.  
What I now want to do is have these images refresh automatically so i can view each frame sequentially without having to refresh over and over. I figured I need to stream these images as mjpegs. From what I understand, I first have to set content type to `multipart/x-mixed-replace` and then respond with content type `image/jpeg` for each image. How do I go about doing that in boost/beast? Any help/advice would be appreciated.  
  
The following is my code:  
```  
#include <iostream>  
#include <string>  
#include <memory>  
#include <thread>  
#include <cstdlib>  
  
#include <opencv2/opencv.hpp>  
#include <opencv2/highgui/highgui.hpp>  
#include <opencv2/imgproc/imgproc.hpp>  
#include <opencv2/core/core.hpp>  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/config.hpp>  
  
using boost::asio::ip::tcp;  
namespace http = boost::beast::http;  
  
cv::Mat frame;  
std::vector<uchar> buffer;  
  
int main(){  
    try{  
        boost::asio::io_context io_service;  
        tcp::acceptor acceptor(io_service, tcp::endpoint(tcp::v4(), 1112));  
        boost::system::error_code err;  
        tcp::socket socket(io_service);  
        acceptor.accept(socket);  
  
        boost::beast::flat_buffer request_buffer;  
  
        cv::VideoCapture cap("zz.mp4");  
  
        while(cap.isOpened()){  
            cap >> frame;  
            cv::imencode(".jpg", frame, buffer, std::vector<int> {cv::IMWRITE_JPEG_QUALITY, 95});  
              
            auto const size = buffer.size();  
  
            http::request<http::string_body> req;  
            http::read(socket, request_buffer, req, err);  
            if(err){  
                std::cerr << "read: " << err.message() << "\n";  
                break;  
            }   
  
            if(req.method() == http::verb::head){  
                http::response<http::empty_body> res{http::status::ok, req.version()};  
                res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
                res.set(http::field::content_type, "image/jpeg");  
                res.content_length(size);  
                res.keep_alive(req.keep_alive());  
                http::write(socket, res, err);  
            }  
  
            http::response<http::vector_body<unsigned char>> res{std::piecewise_construct,  
                        std::make_tuple(std::move(buffer)),  
                        std::make_tuple(http::status::ok, req.version())};  
            res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
            res.set(http::field::content_type, "image/jpeg");  
            res.content_length(size);  
            res.keep_alive(req.keep_alive());  
            http::write(socket, res, err);  
        }  
    }  
    catch(std::exception& e)  
    {  
        std::cerr << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return 0;  
}  
```

---

## Comment 1

> Username: rakhecha42  
> Created at: 2019-10-22 08:21:58 UTC  
> Updated at: 2019-10-22 08:35:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-544855418  

I think what I need to know is how to add boundary in boost beast and what to set the body type as.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-10-22 11:53:57 UTC  
> Updated at: 2019-10-22 11:54:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-544925878  

You can use `http::response<http::string_body> res;` and then `res.body()` will be `std::string` which you can modify as you see fit.

---

## Comment 3

> Username: rakhecha42  
> Created at: 2019-10-22 13:24:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-544960273  

Hello. Sorry I'm unable to understand how to use this to send header for `multipart/x-mixed-replace`.  
Can you please elaborate?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-10-22 14:15:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-544984186  

The specifics of the multipart encoding are outside the scope of Beast. You need to study the documentation for this encoding and then implement your own code to emit the proper string for the body: https://en.wikipedia.org/wiki/MIME#Mixed-Replace_.28experimental.29

---

## Comment 5

> Username: rakhecha42  
> Created at: 2019-10-24 07:09:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-545778437  

Okay sorry about that. I'm closing the issue and leaving the answer just in case someone else requires it. To set the boundary end,  use `boost::beast::http::field::body`  
  
```  
#include <iostream>  
#include <string>  
#include <memory>  
#include <thread>  
#include <cstdlib>  
  
#include <opencv2/opencv.hpp>  
#include <opencv2/highgui/highgui.hpp>  
#include <opencv2/imgproc/imgproc.hpp>  
#include <opencv2/core/core.hpp>  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/config.hpp>  
  
using boost::asio::ip::tcp;  
namespace http = boost::beast::http;  
  
cv::Mat frame;  
std::vector<uchar> buffer;  
cv::Mat grayframe;  
  
int main(){  
    try{  
        boost::asio::io_context io_service;  
        tcp::acceptor acceptor(io_service, tcp::endpoint(tcp::v4(), 1112));  
        boost::system::error_code err;  
        tcp::socket socket(io_service);  
        acceptor.accept(socket);  
  
        boost::beast::flat_buffer request_buffer;  
  
        cv::VideoCapture cap("zz.mp4");  
  
        http::request<http::string_body> req;  
        http::read(socket, request_buffer, req, err);  
        if(err){  
            std::cerr << "read: " << err.message() << "\n";  
        }   
  
        http::response<http::empty_body> res{http::status::ok, req.version()};  
        res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
        res.set(http::field::content_type, "multipart/x-mixed-replace; boundary=frame");  
        res.keep_alive();  
        http::response_serializer<http::empty_body> sr{res};  
        http::write_header(socket, sr);  
  
        while(cap.isOpened()){  
            cap >> frame;  
            cv::cvtColor(frame, grayframe, CV_BGR2GRAY);  
            cv::imencode(".jpg", grayframe, buffer, std::vector<int> {cv::IMWRITE_JPEG_QUALITY, 95});  
              
            auto const size = buffer.size();  
  
            http::response<http::vector_body<unsigned char>> res{std::piecewise_construct,  
                        std::make_tuple(std::move(buffer)),  
                        std::make_tuple(http::status::ok, req.version())};  
            res.set(http::field::body, "--frame");  
            res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
            res.set(http::field::content_type, "image/jpeg");  
            res.content_length(size);  
            res.keep_alive(req.keep_alive());  
            http::write(socket, res, err);  
        }  
    }  
    catch(std::exception& e)  
    {  
        std::cerr << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return 0;  
}  
```

---

## Comment 6

> Username: kasru  
> Created at: 2021-09-18 00:15:50 UTC  
> Updated at: 2021-09-18 00:18:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-922143751  

@rakhecha42 Your solution doesn't work in VLC player, I did it like this.  
  
```cpp  
#include <iostream>  
#include <string>  
#include <memory>  
#include <thread>  
#include <cstdlib>  
  
#include <opencv2/opencv.hpp>  
#include <opencv2/highgui/highgui.hpp>  
#include <opencv2/imgproc/imgproc.hpp>  
#include <opencv2/core/core.hpp>  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/config.hpp>  
  
using boost::asio::ip::tcp;  
namespace http = boost::beast::http;  
  
cv::Mat frame;  
std::vector<uchar> buffer;  
cv::Mat grayframe;  
  
int main(){  
    try{  
        boost::asio::io_context io_service;  
        tcp::acceptor acceptor(io_service, tcp::endpoint(tcp::v4(), 1112));  
        boost::system::error_code err;  
        tcp::socket socket(io_service);  
        acceptor.accept(socket);  
  
        boost::beast::flat_buffer request_buffer;  
  
        cv::VideoCapture cap("zz.mp4");  
  
        http::request<http::string_body> req;  
        http::read(socket, request_buffer, req, err);  
        if(err){  
            std::cerr << "read: " << err.message() << "\n";  
        }   
  
        http::response<http::empty_body> res{http::status::ok, req.version()};  
        res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
        res.set(http::field::content_type, "multipart/x-mixed-replace; boundary=frame");  
        res.keep_alive();  
        http::response_serializer<http::empty_body> sr{res};  
        http::write_header(socket, sr);  
  
        while(cap.isOpened()){  
            cap >> frame;  
            cv::cvtColor(frame, grayframe, CV_BGR2GRAY);  
            cv::imencode(".jpg", grayframe, buffer, std::vector<int> {cv::IMWRITE_JPEG_QUALITY, 95});  
              
            auto const size = buffer.size();  
  
            // do not use http::response<>  
            // hack: write to socket the multipart message  
            std::string message { "\r\n--frame\r\nContent-Type: image/jpeg\r\nContent-Length: " };  
            message += std::to_string( size );  
            message += "\r\n\r\n";  
            auto bytesSent = socket.send( boost::asio::buffer( message ), 0, err );  
            if( !bytesSent )  
            {  
                break;  
            }  
            bytesSent = socket.send( boost::asio::buffer( buffer ), 0, err );  
            if( !bytesSent )  
            {  
                break;  
            }  
        }  
    }  
    catch(std::exception& e)  
    {  
        std::cerr << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return 0;  
}  
```

---

## Comment 7

> Username: esuig  
> Created at: 2022-01-09 13:17:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-1008296534  

I know the issue is closed but I would like to ask two questions: why don't you use beast::websocket? and then, how is the client code like in this case? i.e. which html tag do you use to host the mjpeg video on the client side?

---

## Comment 8

> Username: kasru  
> Created at: 2022-01-09 13:43:09 UTC  
> Updated at: 2022-01-09 13:43:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-1008300720  

> I know the issue is closed but I would like to ask two questions: why don't you use beast::websocket? and then, how is the client code like in this case? i.e. which html tag do you use to host the mjpeg video on the client side?  
  
Html tag "img"

---

## Comment 9

> Username: esuig  
> Created at: 2022-01-10 00:30:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-1008457635  

Thank you, is it possible for you to post the client code?

---

## Comment 10

> Username: kasru  
> Created at: 2022-01-11 17:11:35 UTC  
> Updated at: 2022-01-11 17:11:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1740#issuecomment-1010179567  

> Thank you, is it possible for you to post the client code?  
  
Simple URL:  
`http://localhost:1112`  
or in HTML code:  
`<img src="http://localhost:1112" />`
