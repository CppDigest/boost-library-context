# #2049 - Post request to pastebin [Closed]

> Username: a1987zz  
> Created at: 2020-08-12 02:42:48 UTC  
> Updated at: 2020-08-23 22:20:11 UTC  
> Closed at: 2020-08-23 22:20:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2049  

I send a post request via boost to the pastebin api, but an empty string is returned. Although earlier - two weeks ago, a line of insertion from pastеbin was returned. Here is their api: https://pastebin.com/doc_api:  
  
the string to return: 1VeKw3nnOC.  
https://pastebin.com/dCQfUW4m.  
  
no matter how I tried to change the code, nothing comes out  
  
`  
#include <boost/beast.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio.hpp>  
#include < iostream >  
#include < string >  
#include < locale >  
#include < sstream >  
#include < Windows.h >  
  
   
namespace http = boost::beast::http;  
using namespace std;  
using namespace boost::asio::ip;  
using namespace boost::locale;  
   
   
   
int main() {  
    const std::string host = "pastebin.com";  
    const std::string target = "/api/api_raw.php";  
    string output;  
   
    boost::asio::io_context ioc;  
   
    
    boost::asio::ip::tcp::resolver resolver(ioc);  
   
    boost::asio::ip::tcp::socket socket(ioc);  
   
    boost::asio::connect(socket, resolver.resolve(host, "80"));  
   
    http::request<http::string_body> req{ http::verb::post, target, 11 };  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    req.set(http::field::server, "Beast");  
    req.body() = std::string("api_dev_key=180586b396d0cc8ed21d89b8d64625d4&api_user_key=e327521c51591b66f2793a96859a0ab1&api_paste_key=dCQfUW4m&api_option=show_paste");  
    req.set(http::field::content_type, "application/x-www-form-urlencoded");  
    req.set(http::field::content_length, 135);  
    req.prepare_payload();  
   
    http::write(socket, req);  
   
    {  
        boost::beast::flat_buffer buffer;  
        http::response<http::dynamic_body> res;  
        http::read(socket, buffer, res);  
        std::string output = boost::beast::buffers_to_string(res.body().data());  
   
        std::cout << output << std::endl;  
       // std::wstring wstr(output.size(), L' ');  
       // char const* p = &output[0];  
       // mbsrtowcs(&wstr[0], &p, wstr.size(), nullptr);  
       // std::wcout << wstr << std::endl;  
   
        system("Pause");  
    }  
   
    
    socket.shutdown(boost::asio::ip::tcp::socket::shutdown_both);  
   
    return 0;  
}`  
  
I am attaching ahk code that returns a string, as an example:  
  
api_dev_key := "180586b396d0cc8ed21d89b8d64625d4"  
api_user_key := "e327521c51591b66f2793a96859a0ab1"  
api_paste_key := "dCQfUW4m"  
api_option := "show_paste"  
  
raw_paste := "api_dev_key=" . api_dev_key . "&api_user_key=" . api_user_key . "&api_paste_key=" . api_paste_key . "&api_option=" . api_option  
  
url := "https://pastebin.com/api/api_raw.php"  
  
HttpObj := ComObjCreate("WinHttp.WinHttpRequest.5.1")  
HttpObj.Open("POST", url, false)  
HttpObj.SetRequestHeader("Content-Type", "application/x-www-form-urlencoded")  
HttpObj.Send(raw_paste)  
HttpObj.WaitForResponse()  
Body := HttpObj.ResponseBody  
pData := NumGet(ComObjValue(Body)+8+A_PtrSize)  
ResponseText := StrGet(pData, Body.MaxIndex() + 1, "utf-8")  
msgbox % ResponseText

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-12 02:52:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2049#issuecomment-672518811  

So, you are using a plain socket (`boost::asio::ip::tcp::socket`) and you expect it to work with a server that requires HTTPS? Your "ahk" program connects using HTTPS (`url := "https://pastebin.com/api/api_raw.php"`) but the Beast code you provided uses a plain socket. Try using one of the Beast HTTPS client examples, such as this one:  
https://github.com/boostorg/beast/tree/develop/example/http/client/sync-ssl

---

## Comment 2

> Username: a1987zz  
> Created at: 2020-08-12 03:12:19 UTC  
> Updated at: 2020-08-23 10:12:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2049#issuecomment-672544061  

thanks, but it is difficult for me. Can you please show a complete example of a request, if not difficult.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-12 03:18:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2049#issuecomment-672546509  

Yes I just linked the example which shows how to submit a request using SSL:  
https://github.com/boostorg/beast/tree/develop/example/http/client/sync-ssl

---

## Comment 4

> Username: a1987zz  
> Created at: 2020-08-12 03:41:41 UTC  
> Updated at: 2020-08-12 04:28:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2049#issuecomment-672554280  

Ok, what is int main (int argc, char ** argv) - what is it, you need to pass parameters to the program?

---

## Comment 5

> Username: a1987zz  
> Created at: 2020-08-13 20:14:13 UTC  
> Updated at: 2020-08-15 23:01:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2049#issuecomment-673687820  

ok, why then this code ahk which sends http works? But boost asio is not:  
  
pastekey1 := "dCQfUW4m"  
url := "http://pastebin.com/raw.php?i=" pastekey1  
  
HttpRequest := ComObjCreate("WinHttp.WinHttpRequest.5.1")   
HttpRequest.Open("GET", url)   
HttpRequest.Send()   
InvoiceRawHTML := HttpRequest.ResponseText   
msgbox % InvoiceRawHTML  
  
and in the first example ahk no error is returned: Bad API request, invalid api_dev_key in boost asio if you write http instead of https

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-08-13 20:18:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2049#issuecomment-673689881  

See https://github.com/boostorg/beast/issues/2049#issuecomment-672518811
