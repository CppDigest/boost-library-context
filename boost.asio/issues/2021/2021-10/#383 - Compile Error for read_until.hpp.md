# #383 - Compile Error for read_until.hpp [Open]

> Username: wangxi761  
> Created at: 2021-10-25 10:35:58 UTC  
> Updated at: 2022-05-23 22:56:56 UTC  
> Url: https://github.com/boostorg/asio/issues/383  

a simple code  ,i have tried the version 1.69 and 1.71  
```  
#include <ctime>  
#include <iostream>  
#include <string>  
#include <boost/asio.hpp>  
  
using namespace std;  
using boost::asio::ip::tcp;  
  
string make_daytime_string()  
{  
  
    time_t now = time(0);  
    return ctime(&now);  
}  
  
int main()  
{  
    boost::asio::io_context io_context;  
  
    tcp::acceptor acceptor(io_context, tcp::endpoint(tcp::v4(), 8080));  
  
    try  
    {  
  
        for (;;)  
        {  
            tcp::socket sock(io_context);  
            acceptor.accept(sock);  
            boost::asio::streambuf readBuf;  
            boost::asio::read_until(socket, readBuf, "\n");  
            istream is(&readBuf);  
            cout << is.rdbuf() << endl;  
  
            string str = make_daytime_string();  
            boost::asio::streambuf writeBuf;  
            ostream os(&writeBuf);  
            os << "hello\n";  
            boost::asio::write(sock, writeBuf);  
            sock.close();  
        }  
    }  
    catch (exception &e)  
    {  
        cerr << e.what() << endl;  
    }  
  
    return 0;  
}  
  
```  
compiler with g++  
```  
In file included from /usr/local/include/boost/asio/read_until.hpp:1826:0,  
                 from /usr/local/include/boost/asio.hpp:113,  
                 from demo.cpp:4:  
/usr/local/include/boost/asio/impl/read_until.hpp: In instantiation of ‘std::size_t boost::asio::read_until(SyncReadStream&, const DynamicBuffer&, const string&, boost::system::error_code&) [with SyncReadStream = int(int, int, int) throw (); DynamicBuffer = boost::asio::basic_streambuf_ref<std::allocator<char> >; std::size_t = long unsigned int; std::__cxx11::string = std::__cxx11::basic_string<char>]’:  
/usr/local/include/boost/asio/impl/read_until.hpp:106:45:   required from ‘std::size_t boost::asio::read_until(SyncReadStream&, const DynamicBuffer&, const string&) [with SyncReadStream = int(int, int, int) throw (); DynamicBuffer = boost::asio::basic_streambuf_ref<std::allocator<char> >; std::size_t = long unsigned int; std::__cxx11::string = std::__cxx11::basic_string<char>]’  
/usr/local/include/boost/asio/impl/read_until.hpp:378:20:   required from ‘std::size_t boost::asio::read_until(SyncReadStream&, boost::asio::basic_streambuf<Allocator>&, const string&) [with SyncReadStream = int(int, int, int) throw (); Allocator = std::allocator<char>; std::size_t = long unsigned int; std::__cxx11::string = std::__cxx11::basic_string<char>]’  
demo.cpp:30:58:   required from here  
/usr/local/include/boost/asio/impl/read_until.hpp:200:5: error: request for member ‘read_some’ in ‘s’, which is of non-class type ‘int(int, int, int) throw ()’  
     b.commit(s.read_some(b.prepare(bytes_to_read), ec));  
```

---

## Comment 1

> Username: arthur-tacca  
> Created at: 2022-05-23 22:56:56 UTC  
> Url: https://github.com/boostorg/asio/issues/383#issuecomment-1135214688  

In this line here:  
  
            boost::asio::read_until(socket, readBuf, "\n");  
  
You have used `socket` rather than `sock`. This gives you the C library function `socket()`. You can tell from the error message you posted, which says that   
  
     ... ‘s’, which is of non-class type ‘int(int, int, int) throw ()’  
  
i.e. the first parameter to `read_until()` is a function rather than an instance of a class.
