# #1194 - parse_version() returns bad_version due to HTTP header prefixed with line break. [Closed]

> Username: haquocviet  
> Created at: 2018-07-17 06:34:29 UTC  
> Updated at: 2018-08-21 00:57:03 UTC  
> Closed at: 2018-08-21 00:57:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1194  

BOOST_BEAST_VERSION 167  
IDE: MSVC 2017 15.7   
  
I sometimes see reading data callback returns zero bytes_transferred with error_code = bad_version.  
Digging deeply into your library turns out that the issue is caused by parse_version()  
  
```  
  
boost::beast::http::detail   
{  
class basic_parser_base  
{  
static  
    void  
    parse_version(  
        char const*& it, char const* last,  
        int& result, error_code& ec)  
    {  
	//	Should we remove unwanted line break like this?  
	//while (*it == '\r' || *it == '\n')  
	//	++it;  
			  
        if(it + 8 > last)  
        {  
            ec = error::need_more;  
            return;  
        }  
        if(*it++ != 'H')  
        {  
            ec = error::bad_version;  
            return;  
        }  
		  
		....  
	}  
}  
}  
```  
  
The function would return bad_version code when HTTP header prefixed with "\r\n".  
I don't know why the line break inserted there but somehow it happens.  
Checking by WireShark indicates no sign of line break before "HTTP/1.1" string.  
  
When I do the check at beginning of the parse_version() as below then the issue disappears  
	while (*it == '\r' || *it == '\n')  
		++it;  
  
Would you mind to put a simple check at beginning of the parse_version()?  
Thanks.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-17 14:34:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1194#issuecomment-405604063  

> Would you mind to put a simple check at beginning of the parse_version()?  
  
What you are asking for is to modify the parser to accept non-compliant HTTP. In my opinion this is a bad idea. This change would affect all users and it could have security implications. It also removes any incentive for authors of defective implementations to fix their code.  
  
If you really want to accept non-compliant HTTP, it would be best for you to filter the incoming stream, and apply any transformations you like. An example for doing so may be found here:  
  
https://github.com/boostorg/beast/blob/c4d1bfe7afc16ccf4ec3d6eae93082f810b6d2cb/include/boost/beast/experimental/http/icy_stream.hpp#L23

---

## Comment 2

> Username: haquocviet  
> Created at: 2018-07-18 03:19:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1194#issuecomment-405796190  

Thanks for your recommendation. But as I mentioned in the previous post, I checked raw HTTP header   
captured by WireShark and could not find any line break appearing before "HTTP/1.1". This means HTTP servers I am using are HTTP compliant.  
  
By debugging more deeply I found the cause comes from the asynchronous cancellation of a pending socket leading to receiving buffer (flat_buffer object) could not be cleared completely, I always saw two bytes "\r\n" are left in the buffer. It is tough to find out why the line break is left there from deep inside your library.  
For quickly solving this, I just clear the buffer before passing it to the next async_read().  
  
		m_Response.body().clear();  
		m_Response.clear();  
		m_ResBuffer.consume(m_ResBuffer.size()); // this is what I have added to solve the issue.  
		  
		beast::http::async_read(m_Socket, m_ResBuffer, m_Response,  
			[this](const boost::system::error_code& ec, size_t bytes_transferred) {  
			read_handler(ec, bytes_transferred);  
		});  
  
One thing I wonder why your flat_buffer class does not have clear() function.  
I think it is more compact and nicer to call buff.clear() than buff.consume(buff.size()).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-18 05:35:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1194#issuecomment-405814466  

> I wonder why your flat_buffer class does not have clear() function.  
  
It is not part of the **DynamicBuffer** concept:  
https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/DynamicBuffer.html  
  
> the cause comes from the asynchronous cancellation of a pending socket   
  
Hmm...canceling an asynchronous read or write does not give any guarantees about the amount of data that actually was transferred. It could be nothing, everything, or any value in between. Discarding leftover data can leave the connection in an inconsistent state. For example you may throw out part a partial HTTP header, and never be able to recover.  
  
Usually when you cancel I/O on a socket, you have to close the connection or else the behavior of continued operations on the connection are undefined. This is especially true for OpenSSL connections. You cannot simply cancel a pending I/O than then re-issue a new call.  
  
I advise that you do not cancel I/O unless you are going to close and delete the socket as soon as possible afterwards.

---

## Comment 4

> Username: haquocviet  
> Created at: 2018-07-18 06:42:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1194#issuecomment-405826248  

> Discarding leftover data can leave the connection in an inconsistent state.  
  
I have to do so or my app gets "bad_version" issue otherwise. There is another solution is to destroy and re create entire the HTTP connection object including the flat_buffer object, but that way is too expensive comparing to just discarding the left data. But I may consider better time to clear the buffer instead right before every async_read().  
  
> I advise that you do not cancel I/O unless you are going to close and delete the socket as soon as possible afterwards.  
  
Thanks for your advice. I should consider this.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-08-17 07:20:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1194#issuecomment-413780214  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: haquocviet  
> Created at: 2018-08-21 00:57:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1194#issuecomment-414513956  

Yes, I solved the issue by simply reset each HTTP connection before sending a new request, though it is expensive but it works. Thanks for support.
