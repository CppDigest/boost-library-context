# #2244 - Question by example async-ssl. [Closed]

> Username: StasToken  
> Created at: 2021-05-26 05:21:38 UTC  
> Updated at: 2021-05-26 05:47:47 UTC  
> Closed at: 2021-05-26 05:47:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2244  

Hello dear authors of the project. I apologize for asking this question here, I have been on many forums, but I have no one who could not help with my question. ( please don't swear at me :) ) And I ask for advice here.  
  
I have successfully compiled an  [example from](https://github.com/boostorg/beast/blob/develop/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp) everything works great! but I can't solve the following questions for myself.  
  
I want to get data from the WS server in real time, and put the resulting string in a variable.  
But I don't understand how to organize this data acquisition cycle? - maybe someone has an example?  
That is, I need some kind of event that would trigger the code when the data from the server comes...  
  
Are there any examples? or somewhere you can read how to do it?  
  
_I am using the latest version boost and Visual Studio 2019_

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-26 05:40:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2244#issuecomment-848474398  

Hi @StasToken ,  
  
Using our example as a crib, you would modify the `on_read` method so that it extracts data from the dynamic buffer associated with the `async_read` call.  
  
You will note that in the example, the response to a successful read is to initate an async_close. This is for demonstration purposes only.   
In reality, you would:  
- examine the number of `bytes_transferred`  
- consume these bytes from the dynamic_buffer into your own variable  
- take any action you need to take locally  
- if no error was reported in `ec`, initiate another async_read.  
  
  
Here's a link to a little demonstration cryptocurrency exchange client I wrote for another beast user:  
https://github.com/test-scenarios/boost_beast_websocket_echo/blob/a4dda70ef563f78dca2035e44ba94a38be2295dc/pre-cxx20/fmex_client/connection_base.cpp#L70

---

## Comment 2

> Username: StasToken  
> Created at: 2021-05-26 05:47:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2244#issuecomment-848477780  

Oh, thank you so much! this will help me a lot. :)
