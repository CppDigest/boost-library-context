# #1758 - What version of boost is required ? [Closed]

> Username: McSwan  
> Created at: 2019-11-07 06:31:30 UTC  
> Updated at: 2019-12-15 03:19:19 UTC  
> Closed at: 2019-12-15 03:19:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1758  

I have a current project that uses boost 1.69 - is that good enough to do websocket client and server connections ? Or is it worthwhile upgrading to 1.71?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-07 12:07:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1758#issuecomment-551053075  

1.69 should work, but every new version of Boost contains bug fixes and improvements to Beast, so if you want the "best" version, you should use the latest.

---

## Comment 2

> Username: McSwan  
> Created at: 2019-11-08 02:36:16 UTC  
> Updated at: 2019-11-08 02:38:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1758#issuecomment-551358391  

Hi Vinnie, I'm using the example from 1.66 and they work well.  I changed the "onread" function so it constantly calls itself. I create the connection, then  use the same connection to constantly read from the same source (should be faster than constantly re establishing a new connection)  . Would the connection eventually time out if there is no traffic? Or if there is constant traffic would it stay open indefinitely?  
`void  
    on_read(  
            boost::system::error_code ec,  
            std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "read");  
  
        if (!closeConnection ) {  
            // Read a message into our buffer  
            ws_.async_read(  
                    buffer_,  
                    std::bind(  
                            &Session::on_read,  
                            shared_from_this(),  
                            std::placeholders::_1,  
                            std::placeholders::_2));  
  
            std::cout << boost::beast::buffers(buffer_.data()) << std::endl;  
        }  
        else  
        {  
            // Close the WebSocket connection  
            ws_.async_close(websocket::close_code::normal,  
                            std::bind(  
                                    &Session::on_close,  
                                    shared_from_this(),  
                                    std::placeholders::_1));  
        }  
    }`  
  
ps Congrats on getting beast into boost!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-08 02:36:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1758#issuecomment-551358523  

1.71 has timeouts:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_io/timeouts.html

---

## Comment 4

> Username: McSwan  
> Created at: 2019-11-08 02:41:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1758#issuecomment-551359648  

Cheers - I'll check that out - thanks!

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-12-08 02:42:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1758#issuecomment-562906860  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2019-12-15 03:19:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1758#issuecomment-565773496  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
