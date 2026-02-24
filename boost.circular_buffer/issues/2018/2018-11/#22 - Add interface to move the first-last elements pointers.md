# #22 - Add interface to move the first/last elements pointers [Open]

> Username: reddwarf69  
> Created at: 2018-11-26 10:06:38 UTC  
> Updated at: 2018-11-26 10:08:10 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/22  

I would like to use circular_buffer with Asio. And I'm not the only one -> https://stackoverflow.com/questions/19859833/read-data-into-a-circular-buffer  
  
I could do something like  
  
beast::buffers_cat(  
  asio::buffer(array_one().first, array_one().second),  
  asio::buffer(array_two().first, array_two().second)  
)  
  
to inform asio's async_read() of where it can write the received data to.  
async_read would tell me how many bytes it has actually written into the circular_buffer.... and here I hit the wall. I need an API in circular_buffer where I can tell it "advance the pointer X elements because, even if push_back has not been called, the data is there".
