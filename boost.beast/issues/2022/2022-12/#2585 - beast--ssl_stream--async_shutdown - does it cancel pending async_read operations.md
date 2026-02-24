# #2585 - beast::ssl_stream::async_shutdown - does it cancel pending async_read operations? [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:33:30 UTC  
> Updated at: 2022-12-13 15:53:06 UTC  
> Closed at: 2022-12-13 15:53:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2585  

### Discussed in https://github.com/boostorg/beast/discussions/2390  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **lefterise** February 15, 2022</sup>  
We are wondering if boost::beast::ssl_stream::async_shutdown cancels pending async_read_header / async_read_body operations.  
  
What is the proper way to gracefully cancel any pending async_reads and shutdown the ssl?  
What happens if the other side is not responding to the async_shutdown in a timely manner?</div>
