# #1059 - Multibuffer error after a few hundred messages [Closed]

> Username: jasonpfi  
> Created at: 2018-03-06 14:22:45 UTC  
> Updated at: 2018-03-06 16:07:27 UTC  
> Closed at: 2018-03-06 16:07:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1059  

Here is the repository that shows this bug: https://github.com/jasonpfi/Boost-Multibuffer-Bug  
Please check README.md for more info. I'm using Boost 1.66.0.  
  
Essentially I'm trying to send a few hundred messages very quickly using some python code. The problem is that the multibuffer will simply contain the wrong value at exactly the same point every time. For some reason, the information inside it is being corrupted.   
  
When I switch my implementation to using flatbuffers, this error does not occur.   
  
I have isolated the error to this very line:  
  
const char* buf = boost::asio::buffer_cast<const char*>(boost::beast::buffers_front(buffer));  
  
but I don't know what's wrong with it. Any insight would be appreciated!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-06 15:16:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1059#issuecomment-370814704  

You are looping over 50 bytes, but there is no guarantee that the first buffer in the `multi_buffer` input area will have 50 bytes. It could be 49. Or 1. Only the `flat_buffer` guarantees that the input area will be a single buffer. So this code can produce undefined behavior:  
```  
    for (int i = 0; i < 50; i++)  
```  
  
You need to check the size of the buffer returned by `buffers_front` and only loop over those bytes. If the `multi_buffer` split your message up into more than one buffer, then using `buffers_front` might not be sufficient to access all the bytes. If you want to access all the bytes you can use the iterator adaptor from Asio:  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/buffers_iterator.html  
  
The `multi_buffer` is designed for better performance when appending many pieces of small data, and for when the caller doesn't mind that the data is split up into multiple buffers. The `flat_buffer` is designed for when the caller prefers to have the data in a single contiguous memory buffer, and doesn't mind the additional overhead of the allocate/copy/free cycle caused by appends.

---

## Comment 2

> Username: jasonpfi  
> Created at: 2018-03-06 16:07:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1059#issuecomment-370833041  

Very cool, that makes a lot of sense. Thanks for the insight.  
Closing!
