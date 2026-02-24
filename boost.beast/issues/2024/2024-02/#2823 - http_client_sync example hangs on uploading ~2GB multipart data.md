# #2823 - http_client_sync example hangs on uploading ~2GB multipart data [Open]

> Username: ashjas  
> Created at: 2024-02-15 17:44:09 UTC  
> Updated at: 2024-04-22 07:19:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2823  

**Version of Beast**  
1.71 , but it does not matter, i tried with 1.81 with same behaviour.  
  
**OS: windows**  
  
  
When i try to upload 2GB of data using multipart form data , the testcase hangs although the data gets uploaded successfully as reported by the server.  
  
Here is the reworked http_client_sync.cpp example tweaked to reproduce this.  
  
```  
int main(int argc, char** argv)  
{  
    try  
    {  
        // Check command line arguments.  
        auto const target = "/some/upload/api";  
        int version = 11;  
  
        // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver(ioc);  
        beast::tcp_stream stream(ioc);  
  
        // Look up the domain name  
        auto const results = resolver.resolve("127.0.0.1", "13537");  
  
        // Make the connection on the IP address we get from a lookup  
        stream.connect(results);  
          
        // Set up an HTTP GET request message  
        http::request<http::string_body> req{http::verb::put, target, version};  
  
        //lambda to set the RestRequest fields...  
        auto SetField = [&] (http::field f, std::string headerStr) {  
            if(headerStr.size()) {  
                req.set(f, headerStr);  
            }  
            else if(f == http::field::user_agent) {//Set user-agent as beast version if user provided none.  
                req.set(f, BOOST_BEAST_VERSION_STRING);  
            }  
        };  
        req.keep_alive(false);  
        SetField(http::field::content_type, "multipart/form-data; boundary=ABC-Data");  
        SetField(http::field::host, "127.0.0.1:13537");  
        SetField(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
        SetField(http::field::accept,           "*/*");  
  
        //create the body:  
        std::string body;  
        //some logic to prepare the multipart data..  
        prepare_multipart_data(body);//reads a file, and prepares the multipart data.  
        //results in:  
        /*  
        --ABC-Data  
        Content-Disposition: form-data; name="criteria"  
  
        {"abc":"def"}  
        --ABC-Data  
        Content-Disposition: form-data; name="file"; filename="somefile"  
        Content-Type: application/zip  
  
        some random file data...  
        --ABC-Data--  
        */  
        req.body() = body;  
        req.prepare_payload();  
  
        // Send the HTTP request to the remote host  
        std::cout << req << std::endl;  
        http::write(stream, req);  
  
        // This buffer is used for reading and must be persisted  
        beast::flat_buffer buffer;  
  
        // Declare a container to hold the response  
        http::response<http::dynamic_body> res;  
  
        // Receive the HTTP response  
        http::read(stream, buffer, res);  
  
        // Write the message to standard out  
        std::cout << res << std::endl;  
  
        // Gracefully close the socket  
        beast::error_code ec;  
        stream.socket().shutdown(tcp::socket::shutdown_both, ec);  
  
        // not_connected happens sometimes  
        // so don't bother reporting it.  
        //  
        if(ec && ec != beast::errc::not_connected)  
            throw beast::system_error{ec};  
  
        // If we get here then the connection is closed gracefully  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
```  
Here is a sample req that gets generated and works for file sizes ~1.5GB, but fails for 2GB+ data.  
```  
PUT /some/upload/api HTTP/1.1  
Connection: close  
Content-Type: multipart/form-data; boundary=ABC-Data  
Host: 127.0.0.1:13537  
User-Agent: Boost.Beast/266  
Accept: */*  
Content-Length: 229  
  
--ABC-Data  
Content-Disposition: form-data; name="criteria"  
  
{"abc":"def"}  
--ABC-Data  
Content-Disposition: form-data; name="file"; filename="somefile"  
Content-Type: application/zip  
  
some random file data...  
--ABC-Data--  
```  
  
Any suggestions ?

---

## Comment 1

> Username: ashtum  
> Created at: 2024-02-16 08:02:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-1947919284  

In which line does it hang? Are you certain about the server-side; does it respond with a proper HTTP response after receiving the 2GB request?  
  
If it blocks in the following line, it indicates it's awaiting for the server response, and there might be an issue on that end:  
```C++  
        // Receive the HTTP response  
        http::read(stream, buffer, res);  
```

---

## Comment 2

> Username: ashjas  
> Created at: 2024-02-17 05:28:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-1949657046  

@ashtum   
It blocks on   
```  
http::write(stream, req);  
```  
never returns after this.  
  
at the start netstat shows this:  
```  
netstat | grep 57418  
  TCP    127.0.0.1:13537        noivw-user:57418    ESTABLISHED  
  TCP    127.0.0.1:57418        noivw-user:13537    ESTABLISHED  
```  
After few seconds when the server says 200 OK, netstat shows this:  
```  
netstat | grep 57418  
  TCP    127.0.0.1:13537        noivw-user:57418    FIN_WAIT_2  
  TCP    127.0.0.1:57418        noivw-user:13537    CLOSE_WAIT  
```  
  
If  i match the hashes of the source file and the one downloaded from the server (after 200 OK status )  
they match exactly, so server's upload did finish.  
  
Also, if i analyse the network communication of the test exe through procmon, it appears to be stuck in some infinite loop:  
  
```  
10:51:32.0394554	beastUpload.exe	17384	TCP Send	noivw-user.global.abc.com:57418 -> noivw-user.global.abc.com:13537	SUCCESS	Length: 2147484153, startime: 14837493, endtime: 14838075, seqnum: 0, connid: 0  
10:51:39.8912198	beastUpload.exe	17384	TCP Send	noivw-user.global.abc.com:57418 -> noivw-user.global.abc.com:13537	SUCCESS	Length: 2147484153, startime: 14838282, endtime: 14838860, seqnum: 0, connid: 0  
10:51:47.8166791	beastUpload.exe	17384	TCP Send	noivw-user.global.abc.com:57418 -> noivw-user.global.abc.com:13537	SUCCESS	Length: 2147484153, startime: 14839067, endtime: 14839653, seqnum: 0, connid: 0  
10:51:55.6654320	beastUpload.exe	17384	TCP Send	noivw-user.global.abc.com:57418 -> noivw-user.global.abc.com:13537	SUCCESS	Length: 2147484153, startime: 14839861, endtime: 14840437, seqnum: 0, connid: 0  
10:52:03.4710574	beastUpload.exe	17384	TCP Send	noivw-user.global.abc.com:57418 -> noivw-user.global.abc.com:13537	SUCCESS	Length: 2147484153, startime: 14840643, endtime: 14841218, seqnum: 0, connid: 0  
  
```  
This keeps on going untill i force exit the exe, and thereafter the  netstat entries disappear.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-02-17 05:38:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-1949671520  

I'm unable to replicate this issue on my end. Could you please provide the server-side code?

---

## Comment 4

> Username: ashjas  
> Created at: 2024-02-17 13:59:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-1950216636  

Hi,  
  
I dont think so i can provide the server side code, as its already being used in production.  
The best i could do is adapt boost official examples to replicate the client-side issue.  
  
To add to that, when i use Postman, 3GB upload works fine with the same api, and same server.  
  
And, if you can provide the test-server that you used and tried replicating against,  
i can run my test against that and report back.  
  
Thanks.

---

## Comment 5

> Username: ashjas  
> Created at: 2024-03-06 06:05:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-1980148294  

Hello,  
I am attaching a modified version of the http_server_fast.cpp example along with the client which errors out for 2GB upload.  
The error that i get on windows is:  
```   
An established connection was aborted by the software in your host machine   
```  
Although there is a slight difference between the beast server vs our production server, in the sense that beast gives a proper feedback to the socket and the client exits gracefully, our server somehow does not give the feedback on the socket and the clients gets stuck.  
We could further debug and fix the server, but since it boils down to a behaviour specifically with windows where the upload http packet with ~2GB data is not written on the socket successfully, it would not solve the problem of sending 2GB data in one go on the socket.  
  
Is there any specific limit on windows which is causing this behaviour? and is it configurable by any chance?  
  
**beast_http_server_fast_mod.cpp**  
```  
//  
// Copyright (c) 2017 Christopher M. Kohlhoff (chris at kohlhoff dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: HTTP server, fast  
//  
//------------------------------------------------------------------------------  
  
#include "fields_alloc.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio.hpp>  
#include <chrono>  
#include <cstdlib>  
#include <cstring>  
#include <iostream>  
#include <list>  
#include <memory>  
#include <string>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
// Return a reasonable mime type based on the extension of a file.  
beast::string_view  
mime_type(beast::string_view path)  
{  
    using beast::iequals;  
    auto const ext = [&path]  
    {  
        auto const pos = path.rfind(".");  
        if(pos == beast::string_view::npos)  
            return beast::string_view{};  
        return path.substr(pos);  
    }();  
    if(iequals(ext, ".htm"))  return "text/html";  
    if(iequals(ext, ".html")) return "text/html";  
    if(iequals(ext, ".php"))  return "text/html";  
    if(iequals(ext, ".css"))  return "text/css";  
    if(iequals(ext, ".txt"))  return "text/plain";  
    if(iequals(ext, ".js"))   return "application/javascript";  
    if(iequals(ext, ".json")) return "application/json";  
    if(iequals(ext, ".xml"))  return "application/xml";  
    if(iequals(ext, ".swf"))  return "application/x-shockwave-flash";  
    if(iequals(ext, ".flv"))  return "video/x-flv";  
    if(iequals(ext, ".png"))  return "image/png";  
    if(iequals(ext, ".jpe"))  return "image/jpeg";  
    if(iequals(ext, ".jpeg")) return "image/jpeg";  
    if(iequals(ext, ".jpg"))  return "image/jpeg";  
    if(iequals(ext, ".gif"))  return "image/gif";  
    if(iequals(ext, ".bmp"))  return "image/bmp";  
    if(iequals(ext, ".ico"))  return "image/vnd.microsoft.icon";  
    if(iequals(ext, ".tiff")) return "image/tiff";  
    if(iequals(ext, ".tif"))  return "image/tiff";  
    if(iequals(ext, ".svg"))  return "image/svg+xml";  
    if(iequals(ext, ".svgz")) return "image/svg+xml";  
    return "application/text";  
}  
  
class http_worker  
{  
public:  
    http_worker(http_worker const&) = delete;  
    http_worker& operator=(http_worker const&) = delete;  
  
    http_worker(tcp::acceptor& acceptor, const std::string& doc_root) :  
        acceptor_(acceptor),  
        doc_root_(doc_root)  
    {  
    }  
  
    void start()  
    {  
        accept();  
        check_deadline();  
    }  
  
private:  
    using alloc_t = fields_alloc<char>;  
    //using request_body_t = http::basic_dynamic_body<beast::flat_static_buffer<1024 * 1024>>;  
    using request_body_t = http::string_body;  
  
    // The acceptor used to listen for incoming connections.  
    tcp::acceptor& acceptor_;  
  
    // The path to the root of the document directory.  
    std::string doc_root_;  
  
    // The socket for the currently connected client.  
    tcp::socket socket_{acceptor_.get_executor()};  
  
    // The buffer for performing reads  
    beast::flat_static_buffer<8192> buffer_;  
  
    // The allocator used for the fields in the request and reply.  
    alloc_t alloc_{8192};  
  
    // The parser for reading the requests  
    boost::optional<http::request_parser<request_body_t, alloc_t>> parser_;  
  
    // The timer putting a time limit on requests.  
    net::steady_timer request_deadline_{  
        acceptor_.get_executor(), (std::chrono::steady_clock::time_point::max)()};  
  
    // The string-based response message.  
    boost::optional<http::response<http::string_body, http::basic_fields<alloc_t>>> string_response_;  
  
    // The string-based response serializer.  
    boost::optional<http::response_serializer<http::string_body, http::basic_fields<alloc_t>>> string_serializer_;  
  
    // The file-based response message.  
    boost::optional<http::response<http::file_body, http::basic_fields<alloc_t>>> file_response_;  
  
    // The file-based response serializer.  
    boost::optional<http::response_serializer<http::file_body, http::basic_fields<alloc_t>>> file_serializer_;  
  
    void accept()  
    {  
        // Clean up any previous connection.  
        beast::error_code ec;  
        socket_.close(ec);  
        buffer_.consume(buffer_.size());  
  
        acceptor_.async_accept(  
            socket_,  
            [this](beast::error_code ec)  
            {  
                if (ec)  
                {  
                    accept();  
                }  
                else  
                {  
                    // Request must be fully processed within 60 seconds.  
                    request_deadline_.expires_after(  
                        std::chrono::seconds(60));  
  
                    read_request();  
                }  
            });  
    }  
  
    void read_request()  
    {  
        // On each read the parser needs to be destroyed and  
        // recreated. We store it in a boost::optional to  
        // achieve that.  
        //  
        // Arguments passed to the parser constructor are  
        // forwarded to the message object. A single argument  
        // is forwarded to the body constructor.  
        //  
        // We construct the dynamic body with a 1MB limit  
        // to prevent vulnerability to buffer attacks.  
        //  
        parser_.emplace(  
            std::piecewise_construct,  
            std::make_tuple(),  
            std::make_tuple(alloc_));  
        parser_.get().body_limit(2 * 1024 * 1024 * 1024);  
        http::async_read(  
            socket_,  
            buffer_,  
            *parser_,  
            [this](beast::error_code ec, std::size_t)  
            {  
                if (ec)  
                    accept();  
                else  
                    process_request(parser_->get());  
            });  
    }  
  
    void process_request(http::request<request_body_t, http::basic_fields<alloc_t>> const& req)  
    {  
        switch (req.method())  
        {  
        case http::verb::get:  
            send_file(req.target());  
            break;  
        case http::verb::put:  
            send_file(req.target());  
            break;  
        default:  
            // We return responses indicating an error if  
            // we do not recognize the request method.  
            send_bad_response(  
                http::status::bad_request,  
                "Invalid request-method '" + std::string(req.method_string()) + "'\r\n");  
            break;  
        }  
    }  
  
    void send_bad_response(  
        http::status status,  
        std::string const& error)  
    {  
        string_response_.emplace(  
            std::piecewise_construct,  
            std::make_tuple(),  
            std::make_tuple(alloc_));  
  
        string_response_->result(status);  
        string_response_->keep_alive(false);  
        string_response_->set(http::field::server, "Beast");  
        string_response_->set(http::field::content_type, "text/plain");  
        string_response_->body() = error;  
        string_response_->prepare_payload();  
  
        string_serializer_.emplace(*string_response_);  
  
        http::async_write(  
            socket_,  
            *string_serializer_,  
            [this](beast::error_code ec, std::size_t)  
            {  
                socket_.shutdown(tcp::socket::shutdown_send, ec);  
                string_serializer_.reset();  
                string_response_.reset();  
                accept();  
            });  
    }  
  
    void send_file(beast::string_view target)  
    {  
        // Request path must be absolute and not contain "..".  
        if (target.empty() || target[0] != '/' || target.find("..") != std::string::npos)  
        {  
            send_bad_response(  
                http::status::not_found,  
                "File not found\r\n");  
            return;  
        }  
  
        std::string full_path = doc_root_;  
        full_path.append(  
            target.data(),  
            target.size());  
  
        http::file_body::value_type file;  
        beast::error_code ec;  
        file.open(  
            full_path.c_str(),  
            beast::file_mode::read,  
            ec);  
        if(ec)  
        {  
            send_bad_response(  
                http::status::not_found,  
                "File not found\r\n");  
            return;  
        }  
  
        file_response_.emplace(  
            std::piecewise_construct,  
            std::make_tuple(),  
            std::make_tuple(alloc_));  
  
        file_response_->result(http::status::ok);  
        file_response_->keep_alive(false);  
        file_response_->set(http::field::server, "Beast");  
        file_response_->set(http::field::content_type, mime_type(std::string(target)));  
        file_response_->body() = std::move(file);  
        file_response_->prepare_payload();  
  
        file_serializer_.emplace(*file_response_);  
  
        http::async_write(  
            socket_,  
            *file_serializer_,  
            [this](beast::error_code ec, std::size_t)  
            {  
                socket_.shutdown(tcp::socket::shutdown_send, ec);  
                file_serializer_.reset();  
                file_response_.reset();  
                accept();  
            });  
    }  
  
    void check_deadline()  
    {  
        // The deadline may have moved, so check it has really passed.  
        if (request_deadline_.expiry() <= std::chrono::steady_clock::now())  
        {  
            // Close socket to cancel any outstanding operation.  
            beast::error_code ec;  
            socket_.close();  
  
            // Sleep indefinitely until we're given a new deadline.  
            request_deadline_.expires_at(  
                std::chrono::steady_clock::time_point::max());  
        }  
  
        request_deadline_.async_wait(  
            [this](beast::error_code)  
            {  
                check_deadline();  
            });  
    }  
};  
  
int main(int argc, char* argv[])  
{  
    try  
    {  
        // Check command line arguments.  
        if (argc != 6)  
        {  
            std::cerr << "Usage: http_server_fast <address> <port> <doc_root> <num_workers> {spin|block}\n";  
            std::cerr << "  For IPv4, try:\n";  
            std::cerr << "    http_server_fast 0.0.0.0 80 . 100 block\n";  
            std::cerr << "  For IPv6, try:\n";  
            std::cerr << "    http_server_fast 0::0 80 . 100 block\n";  
            return EXIT_FAILURE;  
        }  
  
        auto const address = net::ip::make_address(argv[1]);  
        unsigned short port = static_cast<unsigned short>(std::atoi(argv[2]));  
        std::string doc_root = argv[3];  
        int num_workers = std::atoi(argv[4]);  
        bool spin = (std::strcmp(argv[5], "spin") == 0);  
  
        net::io_context ioc{1};  
        tcp::acceptor acceptor{ioc, {address, port}};  
  
        std::list<http_worker> workers;  
        for (int i = 0; i < num_workers; ++i)  
        {  
            workers.emplace_back(acceptor, doc_root);  
            workers.back().start();  
        }  
  
        if (spin)  
          for (;;) ioc.poll();  
        else  
          ioc.run();  
    }  
    catch (const std::exception& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
}  
  
```  
**beast_client.cpp**  
```  
//  
// Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: HTTP client, synchronous  
//  
//------------------------------------------------------------------------------  
  
//[example_http_client  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
#include <boost/filesystem.hpp>  
#include <boost/chrono/chrono.hpp>  
  
namespace beast = boost::beast;     // from <boost/beast.hpp>  
namespace http = beast::http;       // from <boost/beast/http.hpp>  
namespace net = boost::asio;        // from <boost/asio.hpp>  
using tcp = net::ip::tcp;           // from <boost/asio/ip/tcp.hpp>  
  
#define BOUNDARY_ABC_DATA					"ABC-Data"  
#define BOUNDARY_START_ABC_DATA				"--ABC-Data\r\n"  
#define BOUNDARY_MID_ABC_DATA				"\r\n--ABC-Data\r\n"  
#define BOUNDARY_END_ABC_DATA				"\r\n--ABC-Data--\r\n"  
#define HEADER_CONTENT_BOUNDARY					" boundary="  
  
#define MULTIPART_BOUNDARY						"boundary"  
#define MULTIPART_NAME_OBJECT					R"(name="object")"  
#define MULTIPART_NAME_FILE_META				R"(name="filemeta")"  
#define MULTIPART_NAME_FILEMETA					"name=\"fileMeta\"\r\n\r\n"  
#define MULTIPART_NAME_PAYLOAD					"name=\"payload\"\r\n\r\n"  
#define MULTIPART_NAME_CRITERIA					"name=\"criteria\"\r\n\r\n"  
#define MULTIPART_NAME_META						"name=\"meta\"\r\n\r\n"  
#define MULTIPART_NAME_FILESTREAM				"name=\"fileStream\"\r\n\r\n"  
  
#define CONTENT_DISPOSITION_FORM_DATA			"Content-Disposition: form-data;"  
#define CONTENT_TYPE_APPLICATION_ZIP			"Content-Type: application/zip\r\n\r\n"  
#define CONTENT_TYPE_MULTIPART_FORMDATA			"multipart/form-data;"  
  
void convertFileToJson(const std::string &attachmentFilePath,  
						std::string &body, std::string& opSaveLogBody)  
{  
	boost::filesystem::path filePath(attachmentFilePath);  
	std::string fileName = filePath.filename().string();  
	std::string buffer = body;//ashsoni: keep copy of input body.  
	buffer.append(CONTENT_DISPOSITION_FORM_DATA);  
	buffer.append(" name=\"file\"; filename=\"" + fileName + "\"" + "\r\n");  
	buffer.append(CONTENT_TYPE_APPLICATION_ZIP);  
	//just for the sake of logging.  
	opSaveLogBody.append(CONTENT_DISPOSITION_FORM_DATA);  
	opSaveLogBody.append(" name=\"file\"; filename=\"" + fileName + "\"" + "\r\n");  
	opSaveLogBody.append(CONTENT_TYPE_APPLICATION_ZIP);  
	boost::filesystem::file_status fs = boost::filesystem::status(attachmentFilePath);  
	if (!boost::filesystem::exists(fs) || (!boost::filesystem::is_regular_file(fs))) {  
		  
	}  
	boost::filesystem::ifstream file(attachmentFilePath.c_str(), std::ios::binary);  
	if (!file) {  
		  
	}  
	try {  
		file.seekg(0, std::ios::end);  
		std::streamsize size = file.tellg();  
		file.seekg(0, std::ios::beg);  
		boost::chrono::steady_clock::time_point m_pStart = boost::chrono::steady_clock::now();  
		std::string fileInString;  
		fileInString.resize(size);  
		file.read(&fileInString[0], fileInString.size());  
		boost::chrono::duration<double> sec = boost::chrono::steady_clock::now() - m_pStart;  
		std::cout << "File read time in seconds:[ " << sec.count() << " ]" << std::endl;  
		body = std::move(fileInString);  
		body.insert(0,buffer);  
	}  
	catch (std::exception& ex) {  
		std::cout << "std::exception in preparing body from file:[ " << ex.what() << " ]" << std::endl;  
	}  
}  
  
// Performs an HTTP GET and prints the response  
int main(int argc, char** argv)  
{  
    try  
    {  
        // Check command line arguments.  
        auto const target = "/abc";  
        int version = 11;  
  
        // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver(ioc);  
        beast::tcp_stream stream(ioc);  
  
        // Look up the domain name  
        auto const results = resolver.resolve("127.0.0.1", "8989");  
  
        // Make the connection on the IP address we get from a lookup  
        stream.connect(results);  
          
        // Set up an HTTP GET request message  
        http::request<http::string_body> req{http::verb::put, target, version};  
  
        //lambda to set the RestRequest fields...  
        auto SetField = [&] (http::field f, std::string headerStr) {  
            if(headerStr.size()) {  
                req.set(f, headerStr);  
            }  
            else if(f == http::field::user_agent) {//Set user-agent as beast version if user provided none.  
                req.set(f, BOOST_BEAST_VERSION_STRING);  
            }  
        };  
        req.keep_alive(false);  
        SetField(http::field::content_type, "multipart/form-data; boundary=ABC-Data");  
        SetField(http::field::host, "127.0.0.1:8989");  
        SetField(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
        SetField(http::field::accept,           "*/*");  
  
        //create the body:  
        std::string body;  
        body.append(BOUNDARY_START_ABC_DATA);  
        //multi-part criteria  
        body.append(CONTENT_DISPOSITION_FORM_DATA);  
        body.append(" ");  
        body.append(MULTIPART_NAME_CRITERIA);  
        std::string criteria = R"({"abc":"def"})";  
        body.append(criteria);  
        body.append(BOUNDARY_MID_ABC_DATA);  
        boost::filesystem::path filePath("D:/2GB");  
        std::string opSaveLogBody;  
        //ADWMessageList outMsgList;  
        convertFileToJson(filePath.string(), body,opSaveLogBody);  
        body.append(BOUNDARY_END_ABC_DATA);  
        req.body() = body;  
        req.prepare_payload();  
  
        // Send the HTTP request to the remote host  
        //std::cout << req << std::endl;  
        std::cout << "Going to write to socket..." << std::endl;  
        http::write(stream, req);  
        std::cout << "Written to socket..." << std::endl;  
        // This buffer is used for reading and must be persisted  
        beast::flat_buffer buffer;  
  
        // Declare a container to hold the response  
        http::response<http::dynamic_body> res;  
  
        // Receive the HTTP response  
        http::read(stream, buffer, res);  
  
        // Write the message to standard out  
        std::cout << res << std::endl;  
  
        // Gracefully close the socket  
        beast::error_code ec;  
        stream.socket().shutdown(tcp::socket::shutdown_both, ec);  
  
        // not_connected happens sometimes  
        // so don't bother reporting it.  
        //  
        if(ec && ec != beast::errc::not_connected)  
            throw beast::system_error{ec};  
  
        // If we get here then the connection is closed gracefully  
    }  
    catch(std::exception const& e)  
    {  
        std::cout << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
  
//]  
  
```  
Thanks.

---

## Comment 6

> Username: ashtum  
> Created at: 2024-03-06 12:29:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-1980764859  

The issue arises from the narrowing conversion occurring in [this line of code](https://github.com/chriskohlhoff/asio/blob/ed5db1b50136bace796062c1a6eab0df9a74f8fa/asio/include/asio/detail/impl/socket_ops.ipp#L1386  
) in Asio, which converts `bytes_transferred` (which is of type `unsigned int`) to `signed_size_type` (a type alias of `int`). That explains why it works fine as long as the size of the buffer is less than 2147483648 bytes.  
  
The [write](https://github.com/chriskohlhoff/asio/blob/ed5db1b50136bace796062c1a6eab0df9a74f8fa/asio/include/asio/impl/write.hpp#L51) function in Asio itself limits the buffer size sent to `write_some`. I believe we should implement something similar in the `beast::http::write` function (or use `asio::write` if possible) .  
  
I'll address this during the time window for bug fixes designated for the upcoming Boost 1.85 release.  
  
By the way, the asynchronous API is functioning properly; you may consider utilizing it until this issue is resolved.

---

## Comment 7

> Username: ashjas  
> Created at: 2024-03-07 07:33:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-1982750307  

using async api would not be possible at the moment, so we will wait for a proper fix for this.  
But as you mentioned, i tried the async api for experimenting.  
Though it works for 2GB , but it fails for 4GB.(looks to be similar issue around 4GB(4294967296 bytes) size handling)  
  
So you would want to revisit the async api aswell for the 1.85 release.  
  
Thanks.

---

## Comment 8

> Username: ashtum  
> Created at: 2024-03-07 18:22:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-1984165941  

Yes, indeed, all variations of HTTP write operations encounter the same issue as they utilize `async_write_some` and `write_some` on the provided stream.  
  
I raised an issue on Asio repository: https://github.com/chriskohlhoff/asio/issues/1443  
  
Lets see what would be the authors response, ideally we expect these type of bugs to resolve in Asio itself, meanwhile you might consider using the following code as a workaround for synchronous HTTP writes:  
  
```C++  
template<  
    class SyncWriteStream,  
    bool isRequest, class Body, class Fields>  
std::size_t  
write2(  
    SyncWriteStream& stream,  
    boost::beast::http::serializer<isRequest, Body, Fields>& sr,  
    boost::system::error_code& ec)  
{  
    static_assert(boost::beast::is_sync_write_stream<SyncWriteStream>::value,  
        "SyncWriteStream type requirements not met");  
    sr.split(false);  
    std::size_t bytes_transferred = 0;  
    if(! sr.is_done())  
    {  
        boost::beast::http::detail::write_lambda<SyncWriteStream> f{stream};  
        do  
        {  
            sr.next(ec, f);  
            bytes_transferred += f.bytes_transferred;  
            if(ec)  
                return bytes_transferred;  
            sr.consume(f.bytes_transferred);  
        }  
        while(! sr.is_done());  
    }  
    else  
    {  
        ec = {};  
    }  
    return bytes_transferred;  
}  
  
template<  
    class SyncWriteStream,  
    bool isRequest, class Body, class Fields>  
std::size_t  
write2(  
    SyncWriteStream& stream,  
    boost::beast::http::serializer<isRequest, Body, Fields>& sr)  
{  
    static_assert(boost::beast::is_sync_write_stream<SyncWriteStream>::value,  
        "SyncWriteStream type requirements not met");  
    boost::beast::error_code ec;  
    auto const bytes_transferred =  
        write2(stream, sr, ec);  
    if(ec)  
        BOOST_THROW_EXCEPTION(beast::system_error{ec});  
    return bytes_transferred;  
}  
```

---

## Comment 9

> Username: ashjas  
> Created at: 2024-04-22 04:10:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-2068451477  

Did we manage to make this fix in 1.85 release?

---

## Comment 10

> Username: ashtum  
> Created at: 2024-04-22 07:19:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2823#issuecomment-2068662002  

I would suggest using [http::serializer::limit](https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/ref/boost__beast__http__serializer/limit.html) to set a limit on the size of the returned `BufferSequence` (note that this is different from `body_limit` and doesn't affect the maximum message size you can send).   
  
```C++  
http::response_serializer<http::string_body> sr{ response };  
sr.limit(1 << 24);  
http::write(stream, sr);  
```  
  
I'm still waiting for a response from Asio's author on this issue. Preferably, this issue should be resolved in Asio itself as it has platform-specific knowledge of limitations (or provide an interface for querying these limitation values).
