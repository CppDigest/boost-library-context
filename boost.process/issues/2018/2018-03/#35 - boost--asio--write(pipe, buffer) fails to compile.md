# #35 - boost::asio::write(pipe, buffer) fails to compile [Closed]

> Username: qwertzui11  
> Created at: 2018-03-12 14:29:28 UTC  
> Updated at: 2018-03-13 08:47:13 UTC  
> Closed at: 2018-03-13 02:12:59 UTC  
> Url: https://github.com/boostorg/process/issues/35  

description:  
I'm trying to write synchronous to a pipe.  
```  
boost::asio::io_context context;  
boost::process::async_pipe pipe{context};  
auto buffer = boost::asio::buffer(data, size);  
boost::asio::write(pipe, buffer);  
```  
but this does not compile, with the error:  
```  
error: no matching function for call to ‘boost::process::detail::posix::async_pipe::write_some(boost::asio::const_buffers_1, boost::system::error_code&)’  
```  
  
However;  
```   
pipe.write_some(buffer);  
```  
DOES compile, but  
```  
boost::system::error_code error;  
pipe.write_some(buffer, error);  
```  
not.  
I'm not finding the `write_some(buffer, error);` method in the documentation:  
http://www.boost.org/doc/libs/develop/doc/html/boost/process/async_pipe.html#idp32419296-bb  
So it seems it doesn't comply to the SyncWriteStream requirements? http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/SyncWriteStream.html  
Is that on purpose?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-03-12 14:40:30 UTC  
> Url: https://github.com/boostorg/process/issues/35#issuecomment-372332796  

Not on purpose, will fix that. How urgent is that for you?

---

## Comment 2

> Username: qwertzui11  
> Created at: 2018-03-12 14:56:58 UTC  
> Updated at: 2018-03-12 14:57:20 UTC  
> Url: https://github.com/boostorg/process/issues/35#issuecomment-372338895  

not that urgent  
my current workaround looks like:  
```  
try {  
  for (std::size_t written{}; written < size;)  
    written += pipe.write_some(buffer);  
} catch (const boost::system::system_error&) {  
}  
```  
  
thx a lot for your fast response and time :+1: :smile:

---

## Comment 3

> Username: qwertzui11  
> Created at: 2018-03-12 14:59:43 UTC  
> Url: https://github.com/boostorg/process/issues/35#issuecomment-372339914  

i guess `boost::asio::read` and `aync_pipe::read_some` got the same issue  
http://www.boost.org/doc/libs/develop/doc/html/boost/process/async_pipe.html#idp32416528-bb  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/SyncReadStream.html

---

## Comment 4

> Username: qwertzui11  
> Created at: 2018-03-13 08:47:13 UTC  
> Url: https://github.com/boostorg/process/issues/35#issuecomment-372589529  

thx for the fix! :smile: :+1:
