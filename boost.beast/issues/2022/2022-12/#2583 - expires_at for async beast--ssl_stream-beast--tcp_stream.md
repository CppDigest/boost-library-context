# #2583 - expires_at for async beast::ssl_stream<beast::tcp_stream> [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:32:22 UTC  
> Updated at: 2022-12-13 15:52:23 UTC  
> Closed at: 2022-12-13 15:52:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2583  

### Discussed in https://github.com/boostorg/beast/discussions/2441  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **moeretm** May 27, 2022</sup>  
Can someone tell me if I'm doing anything wrong when using `expires_at()` for `beast::ssl_stream<beast::tcp_stream>` ?  
I took the example from the repo and modified it to do a `POST` to a server with a 5 second delay while configuring a 4 second timeout. The `beast::tcp_stream` does report a timeout error while the ssl counterpart doesn't. I modified the SSL part to not actually do any SSL for testing purposes.  
[beastasync.txt](https://github.com/boostorg/beast/files/8786829/beastasync.txt)  
 </div>
