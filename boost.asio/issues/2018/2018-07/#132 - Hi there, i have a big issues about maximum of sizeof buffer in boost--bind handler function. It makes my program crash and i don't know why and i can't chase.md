# #132 - Hi there, i have a big issues about maximum of sizeof buffer in boost::bind handler function. It makes my program crash and i don't know why and i can't chase [Closed]

> Username: andrewrada  
> Created at: 2018-07-24 10:25:26 UTC  
> Updated at: 2020-12-30 00:52:28 UTC  
> Closed at: 2020-12-30 00:52:27 UTC  
> Url: https://github.com/boostorg/asio/issues/132  

Here is my tcp socket client  
`class chat_client  
{  
public:  
chat_client(boost::asio::io_service& io_service,  
tcp::resolver::iterator endpoint_iterator)  
: io_service_(io_service),  
socket_(io_service),  
t(io_service)  
{  
boost::asio::async_connect(socket_, endpoint_iterator,  
boost::bind(&chat_client::handle_connect, this,  
boost::asio::placeholders::error));  
}  
void set_timer(boost::posix_time::ptime time, boost::function<void(const boost::system::error_code&)> handler)  
{  
t.expires_at(time);  
t.async_wait(handler);  
}  
  
void write(const chat_message& msg)  
{  
set_timer(boost::posix_time::microsec_clock::universal_time() + boost::posix_time::seconds(1),  
boost::bind(&chat_client::do_write, this, msg, boost::asio::placeholders::error));  
// io_service_.post(boost::bind(&chat_client::do_write, this, msg));  
io_service_.run();  
}  
void do_write(chat_message msg, const boost::system::error_code& error)  
{  
std::cout << "dcm" << std::endl;  
bool write_in_progress = !write_msgs_.empty();  
write_msgs_.push_back(msg);  
if (!write_in_progress)  
{  
boost::asio::async_write(socket_,  
boost::asio::buffer(write_msgs_.front().data(),  
write_msgs_.front().length()),  
boost::bind(&chat_client::handle_write, this,  
boost::asio::placeholders::error,  
boost::asio::placeholders::bytes_transferred));  
}  
}  
  
void handle_write(const boost::system::error_code& error, size_t bytes_transferred)  
{  
if (!error)  
{  
write_msgs_.pop_front();  
if (!write_msgs_.empty())  
{  
boost::asio::async_write(socket_,  
boost::asio::buffer(write_msgs_.front().data(),  
write_msgs_.front().length()),  
boost::bind(&chat_client::handle_write, this,  
boost::asio::placeholders::error,  
boost::asio::placeholders::bytes_transferred));  
}  
}  
else  
{  
do_close();  
}  
}  
  
void do_close()  
{  
socket_.close();  
}  
  
private:  
boost::asio::io_service& io_service_;  
tcp::socket socket_;  
chat_message read_msg_;  
chat_message_queue write_msgs_;  
int engineId;  
bool flag = false;  
bool flag_engine = false;  
response res;  
boost::asio::deadline_timer t;  
};`  
  
---- I generate a function which can push the image to server using write function. request is parsed to chat_message class. Here is my chat_message class  
  
`class chat_message  
{  
public:  
enum { header_length = 7 };  
enum { max_body_length = 0x1FFFFF };  
  
chat_message()  
: body_length_(0)  
{  
}  
  
const char* data() const  
{  
return data_;  
}  
  
char* data()  
{  
return data_;  
}  
  
size_t length() const  
{  
return header_length + body_length_;  
}  
  
const char* body() const  
{  
return data_ + header_length;  
}  
  
char* body()  
{  
return data_ + header_length;  
}  
  
size_t body_length() const  
{  
return body_length_;  
}  
  
void body_length(size_t new_length)  
{  
body_length_ = new_length;  
if (body_length_ > max_body_length)  
body_length_ = max_body_length;  
}  
  
bool decode_header()  
{  
using namespace std; // For strncat and atoi.  
char header[header_length + 1] = "";  
strncat(header, data_, header_length);  
body_length_ = atoi(header);  
if (body_length_ > max_body_length)  
{  
body_length_ = 0;  
return false;  
}  
return true;  
}  
  
void encode_header()  
{  
using namespace std; // For sprintf and memcpy.  
char header[header_length + 1] = "";  
sprintf(header, "%4d", static_cast(body_length_));  
memcpy(data_, header, header_length);  
}  
  
private:  
char data_[header_length + max_body_length];  
size_t body_length_;  
};`  
  
when i set max_body_length >= 655365, it's crashed with segmentation faults at write() function. I doubt that, boost::bind() function have a maximum buffer, but i can't find any informations about that. Can someone help me, thank you guys.

---

## Comment 1

> Username: uecasm  
> Created at: 2018-08-22 02:05:12 UTC  
> Url: https://github.com/boostorg/asio/issues/132#issuecomment-414882711  

You should ask support questions on the mailing list or on StackOverflow, not here.  Issues are for actual verifiable bug reports.  
  
Having said that, your problem is that `chat_message` includes an array directly in its body, and `do_write` accepts that by value, meaning that it's going to be copied around on the stack.  You're probably overflowing the stack as a result.  Use the heap for large memory blocks instead, and avoid unnecessary copies.  
  
Another possible problem is that if `chat_message_queue` might invalidate previous elements on a `push_back` operation (eg. as `vector` does) then a concurrent `write` might crash a previous write operation by deallocating the message out from under it.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:52:26 UTC  
> Url: https://github.com/boostorg/asio/issues/132#issuecomment-752289750  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#630](https://github.com/chriskohlhoff/asio/issues/630).
