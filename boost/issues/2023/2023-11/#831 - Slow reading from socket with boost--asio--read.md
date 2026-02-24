# #831 - Slow reading from socket with boost::asio::read [Closed]

> Username: MortezaBashsiz  
> Created at: 2023-11-22 13:42:16 UTC  
> Updated at: 2023-12-21 12:00:51 UTC  
> Closed at: 2023-12-21 12:00:51 UTC  
> Url: https://github.com/boostorg/boost/issues/831  

Hi  
  
I want to write a request on socket and read the response from it  
The part writing to the socket is OK, but the part read is too slow.  
```c++  
boost::system::error_code ec;  
boost::asio::write(socket, boost::asio::buffer(message), ec);  
if (ec) {  
	std::cerr << ec.what();  
}  
boost::asio::streambuf responseBuffer;  
int bytesTransferred = boost::asio::read(socket, responseBuffer, boost::asio::transfer_all(), ec);  
if (ec && ec != boost::asio::error::eof) {  
	std::cerr << ec.what();  
}  
std::cout << "DEBUG : " << bytesTransferred << std::endl;  
unsigned char tempData[bytesTransferred];  
std::memcpy(tempData, boost::asio::buffer_cast<const void*>(responseBuffer.data()), bytesTransferred);  
```  
  
NOTE: Even with using treansfer_atleast(1) or transfer_exactly(1) in a loop, the result is the same.  
NOTE: I do not know exact size in read and it could be different
