# #414 - Double std::move() in promise. [Open]

> Username: Hentacler  
> Created at: 2023-03-30 23:49:03 UTC  
> Updated at: 2023-03-30 23:49:03 UTC  
> Url: https://github.com/boostorg/asio/issues/414  

Please check the following block:  
  
https://github.com/boostorg/asio/blob/1fa30958e51a4b540b9e95256c4a2c3fc4134c65/include/boost/asio/experimental/impl/promise.hpp#L228  
  
After this std::move(), the completion handler is called with invalid args (line 232).
