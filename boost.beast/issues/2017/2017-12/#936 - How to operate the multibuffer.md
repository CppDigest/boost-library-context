# #936 - How to operate the multibuffer? [Closed]

> Username: lls2wow  
> Created at: 2017-12-10 14:13:10 UTC  
> Updated at: 2019-11-13 14:56:44 UTC  
> Closed at: 2017-12-31 19:23:03 UTC  
> Url: https://github.com/boostorg/beast/issues/936  

Hi @Vinnei Falco,  
  
     I wondered how could I clear the multi buffer in the example websocket-async.cpp . I modified the code based on mine implementation. I read data from server to multi_buffer many times.  I am analog I received the data first  time was  "aaaa",  and second time I received data was "bbbb".  
  
I convert these data to string .  
		stringstream ss;  
		ss << buffers(buf.data());  
        std::string strData = ss.str();  
        std::cout<< strData << endl;  
  
First print is   aaaa  .  That`s OK  
  
Second print  is aaaabbbb.  That`s  unexpected.  
What I mean, I want to know how to clear the multibuffer after received a data .  Or how to clear the multi buffer data before next async read?  I can`t find the function like clear or other function to do this.  
  
Best regards,   
Lv Linsong

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-10 14:17:04 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-350551334  

```  
beast::websocket::stream<boost::asio::ip::tcp::socket> ws{ioc};  
...  
beast::multi_buffer b;  
ws.read(b);  
std::cout << beast::buffers_to_string(b.data()) << std::endl;  
  
// Clear the buffer  
b.consume(b.size());  
```  
  
If you want to get a `std::string`, and you are using the latest Asio (from the boostorg/asio master branch) you can use `boost::asio::dynamic_buffer` to wrap a `std::string` and pass that instead:  
  
```  
beast::websocket::stream<boost::asio::ip::tcp::socket> ws{ioc};  
...  
std::string s;  
auto b = boost::asio::dynamic_buffer(s);  
ws.read(b);  
std::cout << s << std::endl;  
  
// Clear the string  
s.clear();  
```

---

## Comment 2

> Username: lls2wow  
> Created at: 2017-12-10 14:21:39 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-350551630  

@Vinniefalco,  You are very kind , It works . Thank you very much.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-10 14:27:05 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-350551964  

Glad to help!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-12-31 19:23:03 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-354620057  

It looks like this has been resolved so I'll go ahead and close the issue. If you feel that more explanation is needed feel free to re-open it, or to create a new one - thanks!

---

## Comment 5

> Username: rodrigo-p-a  
> Created at: 2018-08-15 20:24:44 UTC  
> Updated at: 2018-08-15 20:25:05 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-413324127  

Hi, can you help me? I try get unsigned char *p; from binary read:  
void  
			on_read(  
				boost::system::error_code ec,  
				std::size_t bytes_transferred)  
		{  
			//boost::ignore_unused(bytes_transferred);  
			if (ec)  
				return fail(ec, "read");  
			// get *p1  pointer to received data...  
			std::size_t s1 = boost::asio::buffer_size(buffer_);  
			unsigned char* p1 = boost::asio::buffer_cast<unsigned char*>(buffer_);  
.....  
  
but i not understand how i can read binary /raw data from websocket

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-08-15 20:54:25 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-413333118  

If `buffer_` is `boost::beast::flat_buffer` then you can write `boost::beast::buffers_front(buffer_).data()` to get a pointer, and `boost::beast::buffers_front(buffer_).size()` to get the size:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__buffers_front.html

---

## Comment 7

> Username: Parean  
> Created at: 2019-11-12 13:57:53 UTC  
> Updated at: 2019-11-12 13:58:33 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-552904204  

@vinniefalco Could you please tell, how can I use the dynamic_string_buffer to read into it several times?   
  
```  
beast::websocket::stream<boost::asio::ip::tcp::socket> ws{ioc};  
...  
std::string s;  
auto b = boost::asio::dynamic_buffer(s);  
ws.read(b);  
std::cout << s << std::endl;  
  
// Clear the string  
s.clear();  
```  
In this example, If I call s.clear(), the next time I read, the s.size returns 0, although s.data() returns received data.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-11-12 14:18:31 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-552912446  

asio's dynamic buffers dont' work with beast yet.

---

## Comment 9

> Username: Parean  
> Created at: 2019-11-12 20:02:39 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-553090462  

I don't quite understand what it means "they don't work".  Now there is no way to read to the buffer more than once, do you mean this?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-11-12 21:36:18 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-553126386  

Yes

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-11-13 14:56:44 UTC  
> Url: https://github.com/boostorg/beast/issues/936#issuecomment-553439299  

See:  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p1100r0.html
