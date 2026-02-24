# #301 - Query about Boost ASIO socket read error [Closed]

> Username: Puruifm  
> Created at: 2019-11-14 11:25:00 UTC  
> Updated at: 2020-12-30 01:11:25 UTC  
> Closed at: 2020-12-30 01:11:25 UTC  
> Url: https://github.com/boostorg/asio/issues/301  

There are two different behaviors exhibited by Boost ASIO library function read_some for the code given below.  
try  
{  
    boost::system::error_code error;  
    :  
   socket->read_some (buffer, error_code);  
   debug_point1  
    :  
}  
catch (...)  
{  
    debug_point2  
    :   
}  
  
After the remote connection socket is closed, sometimes the program flow control comes to debug_point1 with error_code flag set and sometimes it comes to debug_point2.  I tried to search the details in read_some description on boost.org documentation but it's not clear.  
  
When does the socket read_some returns with error code and when does it throw exception ?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:11:23 UTC  
> Url: https://github.com/boostorg/asio/issues/301#issuecomment-752293219  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#732](https://github.com/chriskohlhoff/asio/issues/732).
