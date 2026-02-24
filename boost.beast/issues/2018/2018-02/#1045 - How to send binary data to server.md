# #1045 - How to send binary data to server [Closed]

> Username: lls2wow  
> Created at: 2018-02-27 04:57:52 UTC  
> Updated at: 2018-04-11 17:53:11 UTC  
> Closed at: 2018-03-18 05:45:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1045  

Hi ,@vinniefalco  
    I`m not familiar how to send the binary data to server.  
  
    I test send a byte array to server like this：  
  
```  
    BYTE arrOutput[] = { 0x00, 0x00, 0x00, 0xAA, 0x01, 0x00, 0x02, 0x00, 0x00, 0x00, 0x00, 0x00, 0x7B, 0x22, 0x70, 0x72, 0x6F};  
  
    ......  
    ......  
    		// Send the message  
		ws_.async_write(  
			boost::asio::buffer(arrOutput,sizeof(arrOutput)),  
			std::bind(  
				&session::on_write,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
```  
  
      But always generate the error ：   
        
      **read: websocket connection failed due to a protocol violation**  
  
      Is that I wrap the buffer in a wrong way?  
  
Br  
Lv

---

## Comment 1

> Username: lls2wow  
> Created at: 2018-02-27 05:01:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-368748203  

I use the websocket async demo. Just modified the send content from string to BYTE array

---

## Comment 2

> Username: lls2wow  
> Created at: 2018-02-27 05:05:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-368748714  

```  
	explicit  
		session(boost::asio::io_context& ioc)  
		: resolver_(ioc)  
		, ws_(ioc)  
	{  
		ws_.binary(true);  
	}  
```  
  
I add this code :   
  
`ws_.binary(true);`  
  
it works ok .

---

## Comment 3

> Username: lls2wow  
> Created at: 2018-02-27 05:09:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-368749402  

and another question how to convet the received buffer data to BYTE array ?  
  
Like this ：  
  
```  
	void on_read(  
			boost::system::error_code ec,  
			std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		if (ec)  
			return fail(ec, "read");  
  
		std::cout << text_ << " : " << boost::beast::buffers(buffer_.data()) << std::endl;  
  
                BYTE arraydata[] = **SomefunctionToByteArray**(boost::beast::buffers(buffer_.data()) );  
         }  
```  
  
Br lv

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-02-27 05:19:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-368750856  

If you want to receive the data into a single array of bytes you should use a `beast::flat_buffer`. Then you can use `beast::buffers_front(buffer_.data())` to obtain a `boost::asio::const_buffer`, upon which you can call the `data` and `size` members.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-03-06 14:01:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-370790204  

Has this issue been resolved?

---

## Comment 6

> Username: lls2wow  
> Created at: 2018-03-17 16:27:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-373933149  

I want copy these beast::buffers_front(buffer_.data()) `s  data to BYTE output[] ;  How to do it ?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-03-17 16:31:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-373933429  

Use `boost::asio::buffer_copy`:  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/buffer_copy.html

---

## Comment 8

> Username: lls2wow  
> Created at: 2018-03-17 16:46:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-373934555  

Hi @vinniefalco  ， I`m not familiar  with beast buffer (like flat_buffer multi_buffer).   
  
I tried these code   
```  
char* tempchar = new char[bytes_transferred];  
boost::asio::buffer_copy(tempchar, buffer_, bytes_transferred);  
```  
  
but the second line can`t compile.   
  
It notice need 7 parameter.     
buffer_ is flat_buffer type.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-03-17 16:53:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-373935215  

```  
...  
boost::asio::buffer_copy(tempchar, buffer_.data(), bytes_transferred);  
```  
  
Consider reading the documentation:  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/overview/core/buffers.html  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/ConstBufferSequence.html  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/MutableBufferSequence.html  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/concepts/DynamicBuffer.html

---

## Comment 10

> Username: lls2wow  
> Created at: 2018-03-17 17:11:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-373936590  

Thank you very much . So sorry to trouble you with this simple question. @vinniefalco   
  
But the  code  
`boost::asio::buffer_copy(tempchar, buffer_.data(), bytes_transferred);`  
  
can`t compile either. I `ll study the buffers more from your advised web

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-03-17 18:20:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-373941612  

sorry, `tempchar` is not a **MutableBufferSequence**. It should read:  
```  
...  
boost::asio::buffer_copy(boost::asio::buffer(tempchar, bytes_transferred),  
    buffer_.data(), bytes_transferred);  
```

---

## Comment 12

> Username: lls2wow  
> Created at: 2018-03-18 05:45:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-373974616  

It works good , Thank you @vinnie!

---

## Comment 13

> Username: Gadamatik  
> Created at: 2018-04-11 17:53:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1045#issuecomment-380540100  

Hi @vinniefalco, Thanks so much, this post help me too...   
  
By the way, you did a great work on boost::beast... In the future, it will be better to make 2 books: 1 for beginners in networking and second for advanced (I mean really advance)... I don't know if my suggestion is welcome...  
  
Thanks again.
