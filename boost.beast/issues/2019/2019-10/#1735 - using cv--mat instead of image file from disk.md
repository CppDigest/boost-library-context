# #1735 - using cv::mat instead of image file from disk [Closed]

> Username: rakhecha42  
> Created at: 2019-10-18 11:17:01 UTC  
> Updated at: 2019-10-18 12:04:23 UTC  
> Closed at: 2019-10-18 12:04:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1735  

Hello. First off, amazing repo.   
I wrote this simple code to display an image file in the browser using the sync http client example.  
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
  
int main(){  
    try{  
        boost::asio::io_context io_service;  
        tcp::acceptor acceptor(io_service, tcp::endpoint(tcp::v4(), 1112));  
        boost::system::error_code err;  
  
        boost::beast::flat_buffer buffer;  
  
        http::file_body::value_type body;  
        std::string path = "./x.jpg";  
  
        for (;;){  
            tcp::socket socket(io_service);  
            acceptor.accept(socket);  
  
            // cv::Mat frame = cv::imread("x.jpg");  
            // std::vector<uchar> buff;  
            // cv::imencode(".jpg", frame, buff, std::vector<int> {cv::IMWRITE_JPEG_QUALITY, 95});  
              
            body.open(path.c_str(), boost::beast::file_mode::read, err);  
            auto const size = body.size();  
  
            http::request<http::string_body> req;  
            http::read(socket, buffer, req, err);  
            if(err)  
                std::cerr << "read: " << err.message() << "\n";   
  
            if(req.method() == http::verb::head){  
                http::response<http::empty_body> res{http::status::ok, req.version()};  
                res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
                res.set(http::field::content_type, "image/jpeg");  
                res.content_length(size);  
                res.keep_alive(req.keep_alive());  
                http::write(socket, res, err);  
            }  
  
            http::response<http::file_body> res{std::piecewise_construct,  
                std::make_tuple(std::move(body)),  
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
It worked like a charm. Now, I want to use a cv::mat encoded as jpeg in an unsigned char vector instead of opening a file from disk.   
Any help/advice on how to go about doing that would be appreciated.  
Cheers!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-18 11:49:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1735#issuecomment-543696521  

Use `http::response<http::vector_body<unsigned char>>`

---

## Comment 2

> Username: rakhecha42  
> Created at: 2019-10-18 12:04:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1735#issuecomment-543703399  

Awesome! Thanks a lot!
